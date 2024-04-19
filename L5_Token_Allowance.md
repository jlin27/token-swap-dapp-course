# Set Token Allowance and Check Sufficient Balance

We are now able to fetch a live price, even if the user's wallet is not connected!

Now before we can allow users to ping `/quote` and perform the swap, we will need the user to connect their wallet and approve a token allowance.

A token allowance is required if you want a third-party to move funds on your behalf. In short, you are _allowing_ them to move your tokens.

In our case, we would like the [0x Exchange Proxy smart contract](https://docs.0x.org/introduction/0x-cheat-sheet#exchange-proxy-addresses) to trade our ERC20 tokens for us, so we will need to _approve_ an _allowance_ (a certain amount) for _this contract to move a certain amount of our ERC20 tokens on our behalf_. Read more about [token allowances](https://0x.org/docs/0x-swap-api/advanced-topics/how-to-set-your-token-allowances).

This allowance approval will show up in the user's wallet for them to sign. Here is an example screenshot from MetaMask:

<img width="192" alt="token allowance" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/9743b971-a8bc-4269-8b11-77ee2c64b609">


## Here is the UI we need to setup:


- User selects **sell** and **buy tokens** from the token selectors
- Users inputs a **sellAmount**
- Users can **“Connect Wallet”** powered by ConnectKit
- Whenever sellAmount changes, the **app fetches a price** even if a wallet is not connect
- Displays returned **buyAmount**
- When users find a price they like, they can connect their wallet, and switch to the correct network (e.g. Polygon), the button now says **"Approve** if the user has enough of the sell token
  - The Approval button allows users to set a token allowance
  - This is standard practice when users need to give a token allowance for a third-party to move funds on our behalf, in this case 0x Protocol’s smart contract, specifically the 0x Exchange Proxy to trade the user’s ERC20 tokens on their behalf.
  - Users can set an amount they are comfortable with, the default we’ve set is a really big number, but they can also choose only the amount they have in their wallet
- When the user is happy with this trade, the button is now **“Review Trade** because the user already approved the token allowance, so they can move forward to review the firm quote

To summarize, 

<img width="340" alt="Screenshot 2024-04-19 at 2 09 48 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/05346bc2-8bae-4219-aa7b-18f6195cf246">

_The connected wallet has not approved a token allowance for DAI -> button reads "Approve"_

<img width="350" alt="Screenshot 2024-04-19 at 2 10 02 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/adae9e12-5b93-45c5-965f-1048ec4c466d">

_The connected wallet has already approved a token allowance for WMATIC & has enough balance to proceed -> button reads "Review Trade"_

<img width="366" alt="Screenshot 2024-04-19 at 2 11 54 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/9f601765-5acd-4773-8f5d-caa3337ce6f3">

_The connected wallet has already approved a token allowance for WMATIC, but does not have enough balance to proceed -> button reads "Insufficent Balance"_



## Code

We will need to modify our custom ConnectButton to allow users to approve the token allowance, if it hasn't been approved already. 

The logic to check if the user has approved a token allowance the selected sell token is as follows:

1.  First, we need to check if the spender (0x Exchange Proxy) has an allowance already. We can use wagmi's [useReadContract](https://wagmi.sh/react/api/hooks/useReadContract) hook to read from the sellToken's "allowance" function.

```
// Snippet to be added to /app/components/price.tsx

...
// 1. Read from erc20, does spender (0x Exchange Proxy) have allowance?

const { data: allowance, refetch } =  useReadContract({
address:  sellTokenAddress,
abi:  erc20Abi,
functionName:  "allowance",
args: [takerAddress, exchangeProxy(chainId)],
});
...
```

2.  If there is no allowance, then we will write an approval to the sellToken's smart contract using wagmi's [useSimulateContract](https://wagmi.sh/react/api/hooks/useSimulateContract#usesimulatecontract) and [useWriteContract](https://wagmi.sh/react/api/hooks/useWriteContract#usewritecontract) hooks. For this example, we will set the allowance to a very large number; however, note that it is better practice to only allow just the amount needed to prevent putting the wallet at to much risk.

```
// Snippet to be added to /app/components/price.tsx

...
 // 2. (only if no allowance): write to erc20, approve 0x Exchange Proxy to spend max allowance

    const { data } = useSimulateContract({
      address: sellTokenAddress,
      abi: erc20Abi,
      functionName: "approve",
      args: [exchangeProxy(chainId), MAX_ALLOWANCE],
    });

    // Define writeContractResult for the 'approve' operation
    const {
      data: writeContractResult,
      writeContractAsync: writeContract,
      error,
    } = useWriteContract();

    // useWaitForTransactionReceipt to wait for the approval transaction to complete
    const { data: approvalReceiptData, isLoading: isApproving } =
      useWaitForTransactionReceipt({
        hash: writeContractResult,
      });

    // Call `refetch` when the transaction succeeds
    useEffect(() => {
      if (data) {
        refetch();
      }
    }, [data, refetch]);

    if (error) {
      return <div>Something went wrong: {error.message}</div>;
    }
...
```

3. Update the custom ConnectButton to indicate approval status

```
// Snippet to be added to /app/components/price.tsx

...

    // update button depending if needs approval or approval pending

    if (allowance === 0n) {
      return (
        <>
          <button
            type="button"
            className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full"
            onClick={async () => {
              await writeContract({
                abi: erc20Abi,
                address: sellTokenAddress,
                functionName: "approve",
                args: [exchangeProxy(chainId), MAX_ALLOWANCE],
              });
              refetch();
            }}
          >
            {isApproving ? "Approving…" : "Approve"}
          </button>
        </>
      );

```

4. If allowance approved, we will check if the user has sufficient balance of the sellToken to proceed to make the swap. Based off of this logic, we will the update custom button with the proper message:
   - Show "Insufficient Balance" if the connected wallet does not have enough funds to make the swap
   - Show "Review Trade" if the approval went through and the user can proceed to a firm quote and perform the swap.

```
// Snippet to be added to /app/components/price.tsx

...
    return (
      <button
        type="button"
        disabled={disabled}
        onClick={() => {
          // fetch data, when finished, show quote view
          setFinalize(true);
        }}
        className="w-full bg-blue-500 text-white p-2 rounded hover:bg-blue-700 disabled:opacity-25"
      >
        {disabled ? "Insufficient Balance" : "Review Trade"}
      </button>
    );
  }
```

> Be aware that approvals do cost gas! _Looking for a gasless approach? check out [Gasless API](https://0x.org/docs/tx-relay-api/introduction)._

> Need to quickly revoke an allowance while testing? To revoke an allowance, you can set the allowance to 0. This can be done programmatically or through a UI such as [https://revoke.cash/](https://revoke.cash/).

## Recap

In this lesson, we covered: 
* what is a token allowance
* how to approve a token allowance, specifically for the [0x Exchange Proxy smart contract](https://docs.0x.org/introduction/0x-cheat-sheet#exchange-proxy-addresses)
* how to modify the dApp UI to adjust to whether or not a token allowance approval is needed
* check if the user has sufficient balance to proceed to place the trade


See the full code for this lesson here: 

```
https://github.com/0xProject/token-swap-dapp-course-code/tree/main/L5
```


