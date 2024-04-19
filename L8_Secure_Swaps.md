# Handling Swap Transactions Securely

When building your app, thinking about how to securely handle swap transactions is crucial to forge users' trust and reliability in your application 🤝.

In this section, we will cover secure practices when processing swaps, focusing on, 

* **User authentication** - ensures that only authorized individuals can perform transactions
* **Transaction validation** - verifies the integrity and accuracy of data
* **Error handling** - maintains a seamless user experience by gracefully managing unexpected situations, such as network issues or invalid inputs, thus enhancing the overall security and usability of your web3 application

We will highlight some key mechanisms to ensure secure swap transactions. Note that this is not meant to be a final production-level app, so not all edge cases will be considered.

## User authentication
### 1. Rainbowkit Authentication
Rainbowkit offers [authenticated access](https://www.rainbowkit.com/docs/authentication) to your app, if you do wish to implement it. 

It allows you to optionally enforce that users sign a message with their wallet during the connection process, proving that they own the connected account and allowing you to create an authenticated user session with privileged access to your application.

Additionally, they also allow you to create your own [custom authentication](https://www.rainbowkit.com/docs/custom-authentication) back-end, for example letting users [Sign-in with Ethereum](https://login.xyz/). 

## Transaction validation

When sending pinging the 0x API, we should ensure that the parameters we are sending are valid to ensure the request does not fail. 

### 1. Handle when users don't input numbers in sellAmounts

Since we only want to pass a number to the API, which would result in an error, we want to restrict users from inputting anything other than numbers as a sellAmount. We should restrict the input field to only accept "number" types:

```
In /app/components/price.tsx, PriceView
...
 <input
    id="sell-amount"
    value={sellAmount}
    className="h-9 rounded-md"
    style={{ border: "1px solid black" }}
    type="number"  // Add type="number" here
    onChange={(e) => {
      setTradeDirection("sell");
      setSellAmount(e.target.value);
    }}
  />
...
```
### 2. Check user balance
Another check to include is verifying that the user has enough balance before proceeding to make the quote. 

```
In /app/components/price.tsx, PriceView
...
  // Hook for fetching balance information for specified token for a specific takerAddress
  const { data, isError, isLoading } = useBalance({
    address: takerAddress,
    token: sellTokenObject.address,
  });

  // check whether or not the user has enough balance for the amount they plan to sell
  const inSufficientBalance =
    data && sellAmount
      ? parseUnits(sellAmount, sellTokenDecimals) > data.value
      : true;
...
```
### 3. Validate contract interactions with `useSimulateContract`
When we need to approve the token allowance for the 0x Exchange Proxy, we should run wagmi's [`useSimulateContract`](https://wagmi.sh/react/api/hooks/useSimulateContract)
Hook for simulating and validating a contract interaction. 

One reason to use this hook before directly writing to the contract is that [`useSimulateContract`](https://wagmi.sh/react/api/hooks/useSimulateContract)includes error handling mechanisms to catch and handle transaction-related errors, such as insufficient funds, out-of-gas issues, or transaction rejections. Simulating the transaction beforehand is more cost-effective during development and testing and ensures robustness in handling unexpected scenarios during transaction execution. 

### 4. Gracefully handle writing to contrcts with `useWaitForTransactionReceipt`

[`useWaitForTransactionReceipt`](https://wagmi.sh/core/api/actions/waitForTransactionReceipt) waits for the transaction to be included on a block, and then returns the transaction receipt. If the transaction reverts, then the action will throw an error. Gracefully handling this error improves the UX so users are not left hanging if there was an issue approving the transaction. 

```
In /app/components/price.tsx, PriceView
...
    // useWaitForTransactionReceipt to wait for the approval transaction to complete
    const { data: approvalReceiptData, isLoading: isApproving } =
      useWaitForTransactionReceipt({
        hash: writeContractResult,
      });
...
    if (error) {
      return <div>Something went wrong: {error.message}</div>;
    }
```

## Error handling

### 1. Handling errors from actions
Most all of the actions such as writing approvals to contracts using [`useWriteContract`](https://wagmi.sh/react/api/hooks/useWriteContract) will respond back with an error if there were an issue. Be sure to handle these gracefully for a smooth UX experience. 

```
In /app/components/price.tsx, PriceView
...
  // Define useWriteContract for the 'approve' operation
    const {
      data: writeContractResult,
      writeContractAsync: writeContract,
      error,
    } = useWriteContract();
...
    if (error) {
      return <div>Something went wrong: {error.message}</div>;
    }
```


### 2. Handle error if user rejects the transaction, or the user does not have enough funds to cover the transaction

In QuoteView, after the user clicks "Place Order", the user may reject the transaction, or it turns out that the user does not have enough funds to cover the transaction. In these cases, we can display an error message to the user.

```
In /app/components/quote.tsx
...
import { type BaseError } from 'wagmi' 
...
  const {
    data: hash,
    isPending,
    error,
    sendTransaction,
  } = useSendTransaction();
...
  {error && (
    <div>Error: {(error as BaseError).shortMessage || error.message}</div>
  )}
...
```
