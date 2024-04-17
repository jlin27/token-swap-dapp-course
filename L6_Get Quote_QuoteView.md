# QuoteView

In this lesson, we will setup the QuoteView where users can get a firm quote and finally place their order!

<img width="563" alt="Screenshot 2024-04-16 at 12 27 20 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/dd535749-cdba-4cd2-a843-436e643d6f15">

## What is it?

After the user has made their token selection, inputted the `sellAmount`, and approved the token allowance, we will show them the QuoteView component.

The QuoteView provides users with an overview of the transaction details before executing the token swap. Earlier in the PriceView, we provided users an indicative price because they were just browsing for pricing information, so did not need a full 0x order. On the QuoteView, users are ready to fill the order, so we need to provide them a firm quote, and the Market Makers can know to reserve the proper assets to settle the trade.

## UI/UX Walk-through

- Displays the **sell** and **buy Amounts** that the user pays and receives, respectively
  - I’ve formatted it but you can get the symbols, decimal points for formatting from the token list. The amounts are the buy and sell Amounts returned from the quote
- From here, the user can **“Place Order”** which creates, signs, and sends a new transaction to the network.

## Code

We will setup the code for this component in `/app/component/quote.tsx`. Note it will be very similar to PriceView.

The logic for when the component appears in UI is in [/app/components/quote.tsx](https://github.com/0xProject/token-swap-dapp-course-code/blob/main/L6/app/components/quote.tsx), it is displayed if the user has approved the token allowance, and clicked "Review Trade" to finalize the trade selection.

Some notable sections include, 

### Fetching the firm quote

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

### Submitting the transaction

When the user clicks the "Place Order" button, we can use wagmi's [`sendTransaction`](https://wagmi.sh/core/api/actions/sendTransaction#sendtransaction) hook. We will pull out the required params (gas, to, value) from our quote response and pass them to `sendTransaction`. 

```
      <button
        className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full"
        onClick={() => {
          console.log("submitting quote to blockchain");
          console.log("to", quote.to);
          console.log("value", quote.value);

          sendTransaction &&
            sendTransaction({
              gas: data,
              to: quote?.to,
              value: quote?.value,
            });
        }}
      >
        Place Order
      </button>
```

This will trigger an action for the user to sign the transaction from their wallet. If the user has enough gas and has signed the transaction, the order is submitted to the network. 

## View submitted order on block explorer

Once the order has been successfully submitted, we can view it on a block explorer (e.g. [Polygonscan](https://polygonscan.com/))



