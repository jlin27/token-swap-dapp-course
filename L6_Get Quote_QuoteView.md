# QuoteView

## What is it?

After the user has made their token selection, inputted the sellAmount, and approved the token allowance, we will show them the QuoteView component.

The QuoteView provides users with an overview of the transaction details before executing the token swap. Earlier in the PriceView, we provided users an indicative price because they were just browsing for pricing information, so did not need a full 0x order. On the QuoteView, users are ready to fill the order, so we need to provide them a firm quote, and the Market Makers can know to reserve the proper assets to settle the trade.

<Insert QuoteView screenshot>

## UI/UX Walk-through[

- Displays the **sell** and **buy Amounts** that the user pays and receives, respectively
  - I’ve formatted it but you can get the symbols, decimal points for formatting from the token list. The amounts are the buy and sell Amounts returned from the quote
- From here, the user can **“Place Order”** which creates, signs, and sends a new transaction to the network.

## Code

We will setup the code for this component in `/app/component/quote.tsx`. Note it will be very similar to PriceView.

The logic for when the component appears in UI is in [/pages/index.tsx](https://github.com/0xProject/0x-nextjs-demo-app/blob/main/pages/index.tsx#L18-L24), it is displayed if the user has approved the token allowance, and clicked "Review Trade" to finalize the trade selection.
