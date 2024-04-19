# Monetize Your App

As you build your DeFi business, it’s likely that you are including swaps directly in-app to help your users easily and conveniently trade at the best price. As your business grows, you may consider low-friction ways to monetize in order to generate revenue and build a sustainable Web3 business.

Out-of-the-box with 0x Swap API, you have two monetization options:

- [Collect affiliate fees](https://0x.org/docs/0x-swap-api/guides/monetize-your-app-using-swap#option-1-collect-affiliate-fees) (aka trading fee or commission)
- [Collect trade surplus](https://0x.org/docs/0x-swap-api/guides/monetize-your-app-using-swap#option-2-collect-trade-surplus) (aka positive slippage)

![monetize-swap_1_26](https://github.com/jlin27/token-swap-dapp-course/assets/8042156/2c58ad25-3987-4c40-8bbf-a6f4337bc16c)

_Example of adding affiliate fee in swap widget_

## Option 1: Collect affiliate fees

As a 0x Swap API integrator, you have full flexibility to collect an affiliate fee on any trade made through your application.

Setup requires including the following two parameters when making a Swap API request:

* `feeRecipient` - The ETH address that should receive affiliate fees
* `buyTokenPercentageFee` - The percentage of the buyAmount (tokens received by the user) that should be attributed to feeRecipient (your wallet) as affiliate fees. Denoted as a decimal between 0 - 1.0 where 1.0 represents 100%.

### Example API call

```
https://api.0x.org/swap/v1/quote             // Request a firm quote
?sellToken=DAI                               // Sell DAI
&sellAmount=4000000000000000000000           // Sell amount: 4000 (18 decimal)
&buyToken=ETH                                // Buy ETH
&takerAddress=$USER_TAKER_ADDRESS            // Address that will make the trade
&feeRecipient=$INTEGRATOR_WALLET_ADDRESS     // Wallet address that should receive the affiliate fees
&buyTokenPercentageFee=0.01                  // Percentage of buyAmount that should be attributed as affiliate fees
--header '0x-api-key: [API_KEY]'             // Replace with your own API key
```

When the transaction has gone through, the fee amount will be sent to the `feeRecipient` address you've set. The fee is received in the `buyToken` (the token that the user will receive). If you would like to receive a specific type of token (e.g. USDC), you will need to convert those on your own.

### Displaying fees in the UI

The fee amount is incorporated as part of the quoted price.
Two recommended methods of displaying the fees are:

- display the amount returned by `grossBuyAmount * buyTokenPercentageFee`
- display the `grossBuyAmount` and the `buyTokenPercentageFee` separately
<img width="371" alt="affiliate-fee-example" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/0a142982-1671-4848-9579-e551debaee18">
<img width="589" alt="affiliate-fee-example-2" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/7a3e0b78-3252-41e7-bb06-0f266f581a98">

### Pricing considerations

When deciding how much to set your fee amount, consider the following. We recommend setting your pricing in a way that strengthens your bottom line, aligning it with the value you provide to customers while considering any transaction costs. Note that the additional affiliate fee will impact the price for the end user, so find that sweet spot where your solution remains competitive and impactful.

## Option 2: Collect trade surplus

The second option is to collect trade surplus, aka positive slippage. Trade surplus occurs when the user ends up receiving more tokens than their quoted amount. This occurs in trading when an order is executed at a better price than the initially quoted or expected price at the time the order was placed. This may happen for several reasons including:

* fast-moving markets - volatile markets and prices can lead to favorable pricing
* highly liuqidy markets - more buyers and sellers can lead to more favorable offers and bids
* efficient trading platforms and routes - efficient trading platforms and routes can take advantage of breif moments when prices are more favorable

0x Swap API can be easily configured so that you collect the trade surplus and send that to a specified address. This can be done by setting the `feeRecipientTradeSurplus` parameter in a Swap API request.

`feeRecipientTradeSurplus` represents the wallet address you want to collect the fee in. When a transaction produces trade surplus, 100% of it will be collected in that wallet. The fee is received in the buyToken (the token that the user will receive). If you would like to receive a specific type of token (e.g. USDC), you will need to make that conversion on your own.

`feeRecipientTradeSurplus` represents the wallet address you want to collect the fee in. When a transaction produces trade surplus, 100% of it will be collected in that wallet. The fee is received in the `buyToken` (the token that the user will receive). If you would like to receive a specific type of token (e.g. USDC), you will need to make that conversion on your own.

When `feeRecipientTradeSurplus` is not specified, the feature is effectively OFF and all trade surplus will be passed back to the user.

**Note:** Trade surplus is only sent to `feeRecipientTradeSurplus` for SELLs (i.e. when the sellAmount is specified). It is a no-op for BUYs (i.e. when the buyAmount is specified), which means the user will always receive the trade surplus.

### Example API call

```
https://api.0x.org/swap/v1/quote             // Request a firm quote
?sellToken=DAI                               // Sell DAI
&sellAmount=4000000000000000000000           // Sell amount: 4000 (18 decimal)
&buyToken=ETH                                // Buy ETH
&takerAddress=$USER_TAKER_ADDRESS            // Address that will make the trade
&feeRecipientTradeSurplus=$INTEGRATOR_WALLET_ADDRESS // The recipient of any trade surplus fees
--header '0x-api-key: [API_KEY]'             // Replace with your own API key
```

## Code

Let's add the affiliate fee and trade surplus collection to our app. 

### PriceView
First, let's add it in PriceView. We need to set the following constants:

```
const AFFILIATE_FEE = 0.01; // Percentage of the buyAmount that should be attributed to feeRecipient as affiliate fees
const FEE_RECIPIENT = "0x75A94931B81d81C7a62b76DC0FcFAC77FbE1e917"; // The ETH address that should receive affiliate fees and trade surplus
```

Then, we need to include these when we fetch the price from our `useEffect` hook:
```
 // Fetch price data and set the buyAmount whenever the sellAmount changes
  useEffect(() => {
    const params = {
      sellToken: sellTokenObject.address,
      buyToken: buyTokenObject.address,
      sellAmount: parsedSellAmount,
      buyAmount: parsedBuyAmount,
      takerAddress,
      feeRecipient: FEE_RECIPIENT,  // affiliate fee collection
      buyTokenPercentageFee: AFFILIATE_FEE,  // affiliate fee collection
      feeRecipientTradeSurplus: FEE_RECIPIENT, // trade surplus collection
    };
...
[
    sellTokenObject.address,
    buyTokenObject.address,
    parsedSellAmount,
    parsedBuyAmount,
    chainId,
    sellAmount,
    setPrice,
    FEE_RECIPIENT,
    AFFILIATE_FEE,
  ]);
```

Lastly, we will display it in the UI:

```
         <div className="text-slate-400">
            {price && price.grossBuyAmount
              ? "Affiliate Fee: " +
                Number(
                  formatUnits(
                    BigInt(price.grossBuyAmount),
                    POLYGON_TOKENS_BY_SYMBOL[buyToken].decimals
                  )
                ) *
                  AFFILIATE_FEE +
                " " +
                POLYGON_TOKENS_BY_SYMBOL[buyToken].symbol
              : null}
          </div>
```

Here's how it will look in PriceView:

<img width="437" alt="priceView_with_fee" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/a66188a1-28f3-45f9-8943-3a9e5f6bb109">

### QuoteView

Next, we will update the QuoteView, the changes will be very similar to what we just did in PriceView.

Our constants should be set already:

```
const AFFILIATE_FEE = 0.01; // Percentage of the buyAmount that should be attributed to feeRecipient as affiliate fees
const FEE_RECIPIENT = "0x75A94931B81d81C7a62b76DC0FcFAC77FbE1e917"; // The ETH address that should receive affiliate fees
```

Then, we need to include these when we fetch the price from our `useEffect` hook:
```
// Fetch quote data
  useEffect(() => {
    const params = {
      sellToken: price.sellTokenAddress,
      buyToken: price.buyTokenAddress,
      sellAmount: price.sellAmount,
      takerAddress,
      feeRecipient: FEE_RECIPIENT, // affiliate fee collection
      buyTokenPercentageFee: AFFILIATE_FEE,  // affiliate fee collection
      feeRecipientTradeSurplus: FEE_RECIPIENT, // trade surplus collection
    };
...
[
    price.sellTokenAddress,
    price.buyTokenAddress,
    price.sellAmount,
    takerAddress,
    setQuote,
    FEE_RECIPIENT,
    AFFILIATE_FEE,
  ]);
...
```

Lastly, we will display it in the UI:

```
   <div className="bg-slate-200 dark:bg-slate-800 p-4 rounded-sm mb-3">
      <div className="text-slate-400">
        {quote && quote.grossBuyAmount
          ? "Affiliate Fee: " +
            Number(
              formatUnits(
                BigInt(quote.grossBuyAmount),
                buyTokenInfo(chainId).decimals
              )
            ) *
              AFFILIATE_FEE +
            " " +
            buyTokenInfo(chainId).symbol
          : null}
      </div>
    </div>
```
Here's how it will look in QuoteView:

<img width="367" alt="quoteView_with_fee" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/75b92fcf-8ca7-43f5-a436-8a87659668b8">

That's it for collecting affiliate fees and trade surplus!

## Recap

In this lesson, we cover two out-of-the box ways to easily monetize your swap application - collecting affiliate fees and trade surplus. As our business grows, consider these options in order to generate revenue adn build a sustainble web3 business. 

See final code for this lesson
```
https://github.com/0xProject/token-swap-dapp-course-code/blob/main/L7/app/components/quote.tsx#L20-L21
```




