# Fetch and Display Token List

We currently two drop downs, one to select a _sellToken_ and one to select a _buyToken_.

Where do this list of tokens come from?

Thankfully there are several established sources of curated and open-sourced [Token Lists](https://tokenlists.org/) which standardize lists of ERC20 tokens to filter out high quality, legitimate tokens from scams, fakes, and duplicates. Read more about the importance of token lists [here](https://uniswap.org/blog/token-lists).

As developers, we can choose to ingest the entire list or customize our own token lists based off of these sources.

For ERC20 tokens, these lists typically include crucial metadata - such as the token names (Wrapped Matic), symbol (MATIC), address, and logoURI - which can be leveraged by apps such as ours.

Let's retrieve a list of ERC20 tokens to populate the modal, so that a user can select a token to trade.

## Code

In our demo, we've pre-populated a curated a small token list for you in `/src/constants.ts`.

> In production level apps, it's common practice to maintain a token list since some apps don't support _all_ available tokens.

> For now, we just have tokens from the Polygon chain, but you could easily add more to enable multi-chain support.

All tokens contain the following metadata:

```
name:  string;
address:  string;
symbol:  string;
decimals:  number;
chainId:  number;
logoURI:  string;
```

The tokens are setup in the array `POLYGON_TOKENS` which are accessible via 2 objects, `POLYGON_TOKENS_BY_SYMBOL` and `POLYGON_TOKENS_BY_ADDRESS`. At different points in the dApp, we may need to access the metadata from different keys.

These token lists are called from the dropdown lists in `/app/component/price.tsx`.

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

Once you've added DAI to all the objects, you will be able to select it from the dropdown!

<TODO: Insert screenshot with DAI>

## Other Notable Token Lists

- Tokenlists: [https://tokenlists.org/](https://tokenlists.org/)
- Trust Wallet: [https://github.com/trustwallet/assets/tree/master/blockchains](https://github.com/trustwallet/assets/tree/master/blockchains)
- Polygon Assets github: [https://github.com/maticnetwork/polygon-token-assets/tree/main/assets/tokenAssets](https://github.com/maticnetwork/polygon-token-assets/tree/main/assets/tokenAssets)
- Coin Gecko: [https://tokenlists.org/token-list?url=https://tokens.coingecko.com/uniswap/all.json](https://tokenlists.org/token-list?url=https://tokens.coingecko.com/uniswap/all.json)
