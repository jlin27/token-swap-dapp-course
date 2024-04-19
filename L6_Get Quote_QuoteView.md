# QuoteView

In this lesson, we will setup the QuoteView where users can get a firm quote and finally place their order!

<img width="563" alt="Screenshot 2024-04-16 at 12 27 20 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/dd535749-cdba-4cd2-a843-436e643d6f15">

## What is it?

After the user has made their token selection, inputted the `sellAmount`, and approved the token allowance, we will show them the QuoteView component.

The QuoteView provides users with an overview of the transaction details before executing the token swap. Earlier in the PriceView, we provided users an indicative price because they were just browsing for pricing information, so did not need a full 0x order. On the QuoteView, users are ready to fill the order, so we need to provide them a firm quote, and the Market Makers can know to reserve the proper assets to settle the trade.

## UI/UX Walk-through

Theh UI for the QuoteView should be as follows:
- Displays the **sell** and **buy Amounts** that the user pays and receives, respectively
  - The UI displays the sell and buyAmounts returned by the  `/quote` endpoint. These are formatted based off of the decimal values from the token list. It also shows the token images retrieved from our token list. 
- From here, the user can **“Place Order”** which creates, signs, and sends a new transaction to the network.

## Code

Let's code it up! At a high-level, this is what we will need to code:
* Create a QuoteView component
* Fetch a firm quote
* Send the transaction (using the steps from [wagmi's Send Transaction guide](https://wagmi.sh/react/guides/send-transaction#_3-add-a-form-handler))
### 1. Create a new component and wire it up

Create a new `quote` component that will contain logic to fetch and dispaly a quote and send the transaction. 

```
// Create a new /app/components/quote.tsx
...
export default function QuoteView({
  ...
  return (
    //  Set up UI to display quoted sell and buy token amounts, token symbols, and logos
  );
}
...
```


Next, we need to wire up this new `quote` component and add logic for when it will appear in the UI. This will be set up in [/app/page.tsx]([https://github.com/0xProject/token-swap-dapp-course-code/blob/main/L6/app/components/quote.tsx](https://github.com/0xProject/token-swap-dapp-course-code/blob/main/L6/app/page.tsx). It is displayed if the user has approved the token allowance, and clicked "Review Trade" to finalize the trade selection.

```
// Add QuoteView logic in /app/page.tsx
...
    <div
      className={`flex min-h-screen flex-col items-center justify-between p-24`}
    >
      {finalize && price ? (
        <QuoteView
          takerAddress={address}
          price={price}
          quote={quote}
          setQuote={setQuote}
          chainId={chainId}
        />
      ) : (
        <PriceView
          takerAddress={address}
          setPrice={setPrice}
          setFinalize={setFinalize}
          chainId={chainId}
        />
      )}
    </div>
...
```


### 2. Fetch a firm quote
Fetch a firm quote using the [`useEffect`](https://react.dev/reference/react/useEffect) hook. It will be very similar to how we fetched an indicative price. 

```
 // Fetch quote data
  useEffect(() => {
    const params = {
      sellToken: price.sellTokenAddress,
      buyToken: price.buyTokenAddress,
      sellAmount: price.sellAmount,
      takerAddress,
    };

    async function main() {
      const response = await fetch(`/api/quote?${qs.stringify(params)}`);
      const data = await response.json();
      setQuote(data);
    }
    main();
  }, [
    price.sellTokenAddress,
    price.buyTokenAddress,
    price.sellAmount,
    takerAddress,
    setQuote,
  ]);
```

Next, setup the [route handler](https://nextjs.org/docs/app/building-your-application/routing/route-handlers) for the `/quote` API endpoint

```
// Setup /app/api/quote/route.ts
import { type NextRequest } from "next/server";

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;

  const res = await fetch(
    `https://polygon.api.0x.org/swap/v1/quote?${searchParams}`,
    {
      headers: {
        "0x-api-key": process.env.NEXT_PUBLIC_ZEROEX_API_KEY as string,
      },
    }
  );
  const data = await res.json();

  return Response.json(data);
}
```

### 3. Hook up the `useSendTransaction` hook

To submit our transaction, we will hook up the [`useSendTransaction` hook](https://wagmi.sh/react/api/hooks/useSendTransaction)

```
// In /app/components/quote.tsx
...
  const { data: hash, isPending, sendTransaction } = useSendTransaction();
...
```

When the user clicks the "Place Order" button, we can use wagmi's [`sendTransaction`](https://wagmi.sh/core/api/actions/sendTransaction#sendtransaction) create, sign, and send the transaction. We will need to pull out the required params (gas, to, value, data, gasPrice) from our API quote response and pass them to `sendTransaction`. This will trigger an action for the user to sign the transaction from their wallet. If the user has enough gas and has signed the transaction, the order is submitted to the network. 


```
    <button
        className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full"
        disabled={isPending}
        onClick={() => {
          console.log("submitting quote to blockchain");
          console.log("to", quote.to);
          console.log("value", quote.value);

          sendTransaction &&
            sendTransaction({
              gas: quote?.gas,
              to: quote?.to,
              value: quote?.value, // only used for native tokens
              data: quote?.data,
              gasPrice: quote?.gasPrice,
            });
        }}
      >
```

### 4. Wait for transaction receipt

A submitted transaction usually takes some time to be confirmed on-chain. Thus, we should dispaly the transaction confirmation status to the user by using the [`useWaitForTransactionReceipt` hook](https://wagmi.sh/react/api/hooks/useWaitForTransactionReceipt). 

```
// In /app/components/quote.tsx
...
 const { isLoading: isConfirming, isSuccess: isConfirmed } =
    useWaitForTransactionReceipt({
      hash,
    });
...
return (
      {isConfirming && <div>Waiting for confirmation...</div>} 
      {isConfirmed && <div>Transaction confirmed.</div>}
...
```

## 5. View submitted order on block explorer

`sendTransaction` will return the transaction hash which we can use to redirect users to view the the successfully submitted transaction on the corresponding blockchain explorer.

```
// In /app/components/quote.tsx

  return(
    ...
     {isConfirming && <div>Waiting for confirmation ⏳ ...</div>}
      {isConfirmed && (
        <div>
          Transaction Confirmed! 🎉{" "}
          <a href={`https://https://polygonscan.com/tx/${hash}`}>
            Check Polygonscan
          </a>
        </div>
      )}
    ...
```

Here is an example swap that was made through 0x Exchange Proxy (0.1 WMATIC -> 0.67677 USDC). You can [view this transaction on Polygonscan](https://polygonscan.com/tx/0xf485546ee2a3e556faf7a8a859f96444294c548181aeff78e255a3e0326ba493)!

<img width="1122" alt="polygonscan" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/026cff37-dcde-403f-9db8-848213d0f530">

## Completed L6 Code
```
https://github.com/0xProject/token-swap-dapp-course-code/tree/main/L6
```

## Recap

We now have a completed token swapping dApp from which you can get live pricing and make real swaps that settle on the blockchain 🥳 !

In the next lesson, we will discuss ways you can monetize your app if you wish. 




