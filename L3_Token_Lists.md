# Fetch and Display Token List

Currently our app has two dropdowns, one to select a _sellToken_ and one to select a _buyToken_.

<img width="138" alt="dropdowns" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/1b3f00c2-ba1c-474c-b437-e0e8e2818718">

Where do these lists of tokens come from?

Thankfully there are several established sources of curated and open-sourced [Token Lists](https://tokenlists.org/) which standardize lists of ERC20 tokens to filter out high quality, legitimate tokens from scams, fakes, and duplicates. Read more about the importance of token lists [here](https://uniswap.org/blog/token-lists).

As developers, we can choose to ingest the entire list or customize our own token lists based off of these sources.

For ERC20 tokens, these lists typically include crucial metadata - such as the token names (Wrapped Matic), symbol (MATIC), address, and logoURI - which can be leveraged by apps such as ours.

Let's learn how to retrieve a list of ERC20 tokens to populate the modal, so that a user can select a token to trade.

## Code

In our demo, we've pre-populated a curated a small token list for you in [`/src/constants.ts`](https://github.com/0xProject/token-swap-dapp-course-code/blob/main/L3/src/constants.ts).

> In production level apps, it's common practice to maintain a token list since some apps don't support _all_ available tokens.

> For now, we just have tokens from the Polygon chain, but you could easily add more to enable multi-chain support.

All tokens contain the following metadata:

```
// See /src/constants.ts

name:  string;
address:  Address;
symbol:  string;
decimals:  number;
chainId:  number;
logoURI:  string;
```

The tokens are setup in the array `POLYGON_TOKENS` which are accessible via 2 objects, `POLYGON_TOKENS_BY_SYMBOL` and `POLYGON_TOKENS_BY_ADDRESS`. At different points in the dApp, we may need to access the metadata from different keys.

These token lists are called from the dropdown lists in [`/app/component/price.tsx`](https://github.com/0xProject/token-swap-dapp-course-code/blob/main/L3/app/components/price.tsx#L95-L103).

```
...
          <section className="flex mb-6 mt-4 items-start justify-center">
            <label htmlFor="buy-token" className="sr-only"></label>
            <Image
              alt={buyToken}
              className="h-9 w-9 mr-2 rounded-md"
              src={POLYGON_TOKENS_BY_SYMBOL[buyToken].logoURI}
              width={6}
              height={6}
            />
            <select
              name="buy-token-select"
              id="buy-token-select"
              value={buyToken}
              className="mr-2 w-50 sm:w-full h-9 rounded-md"
              onChange={(e) => handleBuyTokenChange(e)}
            >
              {/* <option value="">--Choose a token--</option> */}
              {POLYGON_TOKENS.map((token) => {
                return (
                  <option
                    key={token.address}
                    value={token.symbol.toLowerCase()}
                  >
                    {token.symbol}
                  </option>
                );
              })}
            </select>
...
```

Note that this is just one way to curate and surface token lists. Other teams may choose to ping an API and dynamically filter the list. The best option is whatever best fits the needs of your project.

## Add a New Token

Let's add DAI to our token list!

Here is the metadata for DAI on Polygon (see details on [PolygonScan](https://polygonscan.com/address/0x8f3cf7ad23cd3cadbd9735aff958023239c6a063)):

```
  {
    chainId: 137,
    name: "Dai - PoS",
    symbol: "DAI",
    decimals: 18,
    address: "0x8f3cf7ad23cd3cadbd9735aff958023239c6a063",
    logoURI:
      "https://raw.githubusercontent.com/maticnetwork/polygon-token-assets/main/assets/tokenAssets/dai.svg",
  },
```

Format it correctly inside `/src/constants.ts` so it is accessible by all our objects. 

Once you've added DAI to all the objects, you will be able to select it from the dropdown!

<img width="336" alt="L3" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/2f995da3-9e9b-4c12-ba6f-3db86e1243c0">


## Other Notable Token Lists

Want to add more token? Check out these open-sourced Token Lists: 

- Tokenlists: [https://tokenlists.org/](https://tokenlists.org/)
- Trust Wallet: [https://github.com/trustwallet/assets/tree/master/blockchains](https://github.com/trustwallet/assets/tree/master/blockchains)
- Polygon Assets github: [https://github.com/maticnetwork/polygon-token-assets/tree/main/assets/tokenAssets](https://github.com/maticnetwork/polygon-token-assets/tree/main/assets/tokenAssets)
- Coin Gecko: [https://tokenlists.org/token-list?url=https://tokens.coingecko.com/uniswap/all.json](https://tokenlists.org/token-list?url=https://tokens.coingecko.com/uniswap/all.json)

Additionally, 0x working on a Token Registry API that provides apps with dynamic, comprehesive, and curated token metadata. The API is currently in progress. [Contact us](https://0x.org/contact) if you would like early access. 

## Recap

In this lesson, we learned about how to source token metadata, curate a token list, and surface this information to users through our UI. 
<img width="422" alt="Screenshot 2024-04-19 at 2 07 34 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/a5992263-0839-4ed0-9132-75d7b741aac8">

See the final code for this lesson here: 

```
https://github.com/0xProject/token-swap-dapp-course-code/tree/main/L3
```
