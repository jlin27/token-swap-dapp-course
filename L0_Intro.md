# Token Swapping in DeFi Applications

## Intro

Have you ever gone onto your favorite token trading dApp, such as Matcha.xyx, to trade DAI for WETH and wondered how did it find the best price for you?

<img width="250" alt="matcha_screenshot" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/3b98c278-8132-46f1-9e9d-8c123ae6ae50">


_Screenshot from [Matcha.xyz](https://matcha.xyz/), a trading dApp that aggregates liquidity_


It is likely powered by a [liquidity aggregator](https://0x.org/post/what-is-a-dex-aggregator).

The reason a liquidity aggregator is important is because given the _decentralized_ nature of DeFi, token liquidity is increasingly fragmented across different chains and many different sources, including _on-chain_ and _off-chain_ sources. Additionally, not every source will have every token, and not every source is easily accessible by all dApps.

<img width="512" alt="fragmented_liquidity" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/23b06ab3-2df4-4d48-8b94-231294962cb3">

Liquidity aggregators sources all the possible prices across **off-chain** (e.g. Market Makers, Orderbooks) and **on-chain** (e.g. DEXs, AMMs) and **routes to find the best price for the user**. It's similar to how Google Flights aggregates price data from various airlines and shows users the best routes.

In this tutorial, we will learn how to use the **[0x Swap API](/0x-swap-api/introduction)** which **aggregates liquidity across 100+ sources** and uses **[smart order routing](https://0x.org/post/0x-smart-order-routing)** to find the best price possible with the **lowest slippage**.

<img width="537" alt="swap_api_benefits" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/4a7497c9-11d9-4758-86fc-f041d5f2c2dd">


<img width="640" alt="swap_api" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/6d6edabb-8314-43b6-a8e8-fdc79102d124">

This is the same endpoint that is behind swaps in major wallets and exchanges such as MetaMask, Coinbase wallet, Zapper, and [many more](https://0x.org/docs/introduction/introduction-to-0x#the-0x-ecosystem).


Note that we won’t need to write any smart contracts to find and settle the trade! Instead, the Swap API allows web3 developers to easily tap into the 0x Protocol smart contracts which takes care of all the logic used to settle a trade, allowing web developers to focus on building the best trade experience.

<img width="539" alt="0x_layers" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/73aae476-cdbd-4105-8a57-84798a8900cc">


## What you will learn

By the end of this tutorial, you will learn the following:

- Understand why **Liquidity Aggregation** is important
- Query and display **ERC20 token lists**
- Use the **0x Swap API**
- Set a **Token Allowance**
- Build a **Simple Token Swap DApp** that allows users to connect their wallet via **RainbowKit**

## What you will build

By the end of this course, you will build a simple dApp for token swaps using [Next.js 13](https://nextjs.org/), [0x Swap API](https://0x.org/docs/0x-swap-api/introduction), and [Rainbowkit](https://github.com/rainbow-me/rainbowkit/tree/main). The interface will allow users to select token pairs, view exchange rates, handle approvals, and execute swaps.

Checkout with live demo 👉 [here](https://0x-nextjs-demo-app-git-main-0x-eng.vercel.app/)

<img width="432" alt="Screenshot 2024-04-19 at 1 59 24 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/6ceba94f-0220-4bf4-b4f8-9d748652eb1f">



## Pre-requisites

Before diving into this walk-through, it's essential to ensure you have the familiarity with the following tools and concepts:

- [React](https://react.dev/)
- [Next.js](https://nextjs.org/)
- [wagmi](https://wagmi.sh/)
- [viem](https://viem.sh/)

Additionally, we will be building our token swap dApp on the [Polygon](https://polygon.technology/) network, which provides a cost-effective environment for experimentation. You can always modify this app to support any of the [9-EVM compatible chains](https://0x.org/docs/introduction/0x-cheat-sheet#swap-api-endpoints) that Swap API is deployed on.

Let's get started!
