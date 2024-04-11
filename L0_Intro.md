# Token Swapping in DeFi Applications

## Intro

Have you ever gone onto your favorite token trading dApps to trade DAI for WETH and wondered how did it find the best price for you?

<TODO: Insert Matcha screenshot Image>

It is likely powered by a [liquidity aggregator](https://0x.org/post/what-is-a-dex-aggregator).

The reason a liquidity aggregator is important is because given the _decentralized_ nature of DeFi, token liquidity is increasingly fragmented across different chains and many different sources, including _on-chain_ and _off-chain_ sources. Additionally, not every source will have every token, and not every source is easily accessible by all dApps.

<TODO: insert fragmented liquidity diagram>

Liquidity aggregators sources all the possible prices across **off-chain** (e.g. Market Makers, Orderbooks) and **on-chain** (e.g. DEXs, AMMs) and **routes to find the best price for the user**. It's similar to how Google Flights aggregates price data from various airlines and shows users the best routes.

In this tutorial, we will learn how to use the **[0x Swap API](/0x-swap-api/introduction)**. This API which **aggregates liquidity across 100+ sources** and uses **[smart order routing](https://0x.org/post/0x-smart-order-routing)** to find the best price possible with the **lowest slippage**.

<TODO: insert 0x swap api diagram>

This is the same endpoint that is behind swaps in major wallets and exchanges such as MetaMask, Coinbase wallet, Zapper, and [many more](https://0x.org/docs/introduction/introduction-to-0x#the-0x-ecosystem).

<TODO: insert 0x layers diagram>

Note that we won’t need to write any smart contracts to find and settle the trade! Instead, the Swap API allows web3 developers to easily tap into the 0x Protocol smart contracts which takes care of all the logic used to settle a trade, allowing web developers to focus on building the best trade experience.

## What you will learn

By the end of this tutorial, you will learn how to do the following:

- Understand why **Liquidity Aggregation** is important
- Query and display **ERC20 token lists** for **multiple chains**
- Use the **0x Swap API**
- Set a **Token Allowance**
- Build a **Simple Token Swap DApp** that connects to **MetaMask** using **RainbowKit**

## What you will build

By the end of this course, you will build a simple dApp for token swaps using [Next.js 13](https://nextjs.org/), [0x Swap API](https://0x.org/docs/0x-swap-api/introduction), and [Rainbowkit](https://github.com/rainbow-me/rainbowkit/tree/main). The interface will allow users to select token pairs, view exchange rates, handle approvals, and execute swaps.

Checkout with live demo 👉 [here](https://0x-nextjs-demo-app-git-main-0x-eng.vercel.app/)

<Insert swap demo gif>

## Pre-requisites

Before diving into this walk-through, it's essential to ensure you have the familiarity with the following tools and concepts:

- [React](https://react.dev/)
- [Next.js](https://nextjs.org/)
- [wagmi](https://wagmi.sh/)
- [viem](https://viem.sh/)

Additionally, we will be building our token swap dApp on the [Polygon](https://polygon.technology/) network, which provides a cost-effective environment for experimentation. You can always modify this app to support any of the [9-EVM compatible chains](https://0x.org/docs/introduction/0x-cheat-sheet#swap-api-endpoints) that Swap API is deployed on.

Let's get started!
