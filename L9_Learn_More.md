# Learn more

## What you've accomplished
Congrats! You've built a token swapping dApp that allows you get retrieve live pricing and execute live swaps on-chain! 🚀
<img width="432" alt="Screenshot 2024-04-19 at 1 59 24 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/92a5a4b7-1210-486f-8b78-01a0761c1dcb">

In this course, you learned how to:

- **Integrate with a token swap API**: Connect your application to access liquidity and token swap functionalities through a DeFi protocol
- **Create a Token Swap Interface**: Design a user interface that allows users to select token pairs, view exchange rates, and execute swaps.
- **Execute a Swap Transaction**: Implement the functionality to execute a swap, handling approvals if necessary, and managing transaction submissions.

## Challenges

Now to take your dapp to the next level! Here are some challenges to try on your own to test your understanding.

- Support additional chains and tokens (remember to update the chains a user can connect to through Rainbowkit)
- Show the percentage breakdown where a swap was sourced from using the `sources` [response param](https://0x.org/docs/0x-swap-api/api-references/get-swap-v1-quote#response) returned from `/quote` (ex: the best price comes from 50% Uniswap, 50% Kyber)
- Currently we set the token allowance is set to the max amount. Change this to be safer so the user only approves just the amount needed.
- Show estimated gas in $
- Use 0x Swap API as part of a multi-step action (e.g. swap + cross-chain, swap in smart contract)
- Restrict the UI from allowing users to select the same sell and buy tokens

> Want to try a gasless approach (gasless approvals + gasless swaps)? Checkout [How to build a dApp with Gasless API (Next.js)
](https://0x.org/docs/tx-relay-api/guides/build-a-dapp-with-tx-relay-api). 🚫⛽️

