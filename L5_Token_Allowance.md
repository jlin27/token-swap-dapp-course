# Set Token Allowance and Check Sufficient Balance

We are now able to fetch a live price, even if the user's wallet is not connected!

Now before we can allow users to ping ping quote and perform the swap, we will need the user to connect their wallet and approve a token allowance.

A token allowance is required if you want a third-party to move funds on your behalf. In short, you are _allowing_ them to move your tokens.

In our case, we would like the [0x Exchange Proxy smart contract](https://docs.0x.org/introduction/0x-cheat-sheet#exchange-proxy-addresses) to trade our ERC20 tokens for us, so we will need to _approve_ an _allowance_ (a certain amount) for _this contract to move a certain amount of our ERC20 tokens on our behalf_. Read more about [token allowances](https://0x.org/docs/0x-swap-api/advanced-topics/how-to-set-your-token-allowances).

<Insert image of token allowance>

### Here is the UI we need to setup:

- User selects **sell** and **buy tokens** from the token selectors
- Users inputs a **sellAmount**
- Users can **“Connect Wallet”** powered by ConnectKit
- Whenever sellAmount changes, the **app fetches a price** even if a wallet is not connect
- Displays returned **buyAmount**
- When users find a price they like, they can connect their wallet, and switch to the correct network (e.g. Polygon), the button now says **"Approve** if the user has enough of the sell token
  - The Approval button allows users to set a token allowance
  - This is standard practice when users need to give a token allowance for a third-party to move funds on our behalf, in this case 0x Protocol’s smart contract, specifically the 0x Exchange Proxy to trade the user’s ERC20 tokens on their behalf.
  - Users can set an amount they are comfortable with, the default we’ve set is a really big number, but they can also choose only the amount they have in their wallet
- , our approve button is now **“Review Trade** because we already approved the token allowance, so we can move forward

<insert token approval gif>

## Code

We will need to modify our custom ConnectButton to allow users to approve the token allowance.

The logic to check if the user has approved a token allowance the selected sell token is as follows:

1.  First, we need to check if the spender (0x Exchange Proxy) has an allowance already. We can use wagmi's useReadContract hook to read from the sellToken's "allowance" function.

```
// Snippet to be added to /app/components/price.tsx

...
// 1. Read from erc20, does spender (0x Exchange Proxy) have allowance?

const { data: allowance, refetch } =  useReadContract({
address:  sellTokenAddress,
abi:  erc20Abi,
functionName:  "allowance",
args: [takerAddress, exchangeProxy(chainId)],
});
...
```

2.  If there is no allowance, then we will write an approval to the sellToken's smart contract using wagmi's useSimulateContract and useWriteContract hooks. For this example, we will set the allowance to a very large number; however, note that it is better practice to only allow just the amount needed to prevent putting the wallet at to much risk.

```
// Snippet to be added to /app/components/price.tsx

...
 // 2. (only if no allowance): write to erc20, approve 0x Exchange Proxy to spend max integer
  const { config } = usePrepareContractWrite({
    address: sellTokenAddress,
    abi: erc20ABI,
    functionName: "approve",
    args: [exchangeProxy, MAX_ALLOWANCE],
  });

  const {
    data: writeContractResult,
    writeAsync: approveAsync,
    error,
  } = useContractWrite(config);

  const { isLoading: isApproving } = useWaitForTransaction({
    hash: writeContractResult ? writeContractResult.hash : undefined,
    onSuccess(data) {
      refetch();
    },
  });

  if (error) {
    return <div>Something went wrong: {error.message}</div>;
  }

  if (allowance === 0n && approveAsync) {
    return (
      <>
        <button
          type="button"
          className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full"
          onClick={async () => {
            const writtenValue = await approveAsync();
          }}
        >
          {isApproving ? "Approving…" : "Approve"}
        </button>
      </>
    );
  }
...
```

3. Update the custom ConnectButton to display the proper message:
   - "Insufficient Balance" if the connected wallet does not have enough funds to make the swap
   - "Review Trade" if the approval went through and the user can proceed to a firm quote and perform the swap.

```
// Snippet to be added to /app/components/price.tsx

...
 // 3. update button message depending if connected wallet has enough funds
  return (
    <button
      type="button"
      disabled={disabled}
      onClick={onClick}
      className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full disabled:opacity-25"
    >
      {disabled ? "Insufficient Balance" : "Review Trade"}
    </button>
  );
}
```

> Be aware that approvals do cost gas! _Looking for a gasless approach? check out [Gasless API](https://0x.org/docs/tx-relay-api/introduction)._

> Need to quickly revoke an allowance while testing? To revoke an allowance, you can set the allowance to 0. This can be done programmatically or through a UI such as [https://revoke.cash/](https://revoke.cash/).
