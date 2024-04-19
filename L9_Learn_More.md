# Learn more

Congrats! You've built a token swapping dApp that allows you get retrieve live pricing and execute live swaps on-chain! 🚀

Now to take your dapp to the next level! Here are some challenges to try on your own to test your understanding!

- Add other chains and other tokens
  - add new changes in providers.tsx for rainbowkit ConnectButton
- Show the percentage breakdown where a swap was sourced from using the `sources` [response param](https://0x.org/docs/0x-swap-api/api-references/get-swap-v1-quote#response) (ex: the best price comes from 50% Uniswap, 50% Kyber)
- Currently we set the token allowance is set to the max amount. Change this to be safer so the user only approves just the amount needed.
- Show estimated gas in $
- Use 0x Swap API as part of a multi-step action (e.g. swap + cross-chain, swap in smart contract)
- Restrict the UI from allowing users to select the same sell/buyTokens

