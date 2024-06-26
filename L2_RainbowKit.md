# RainbowKit 🌈

In this lesson, we will learn how to allow users to connect their wallets to our dApp using RainbowKit. 

## What is it?

[RainbowKit](https://www.rainbowkit.com/) is a React library that makes it easy to add wallet connection to your dApp. We are leveraging their [Next.js App Router example](https://www.rainbowkit.com/docs/installation#further-examples) in our example. 

Check out their [installation instructions](https://www.rainbowkit.com/docs/installation) for full details on the imports, configuration, and providers. You can [configure](https://www.rainbowkit.com/docs/installation#configure) your desired chains and [customize the button UI](https://www.rainbowkit.com/docs/custom-connect-button), which we will demonstrate in this dApp.

## Setup WalletConnect `projectId` in providers.tsx

RainbowKit relies on WalletConnect which requires a `projectId`. To get one, go to [WalletConnect Cloud](https://cloud.walletconnect.com/). This is absolutely free and only takes a few minutes. Add it into the `/app/providers.tsx` file. 

```
// /app/providers.tsx file

const config = getDefaultConfig({
   ...
   projectId: "YOUR_WALLETCONNECT_PROJECT_ID",
```

## Add ConnectButton to top right

Typically, apps have a connect wallet button at the top of the app. Let's add a standard ConnectButton to the top right. 

First, setup the imports in `page.tsx` and `components/price`

```
import { ConnectButton } from '@rainbow-me/rainbowkit';
```

Then add the button in the header of `components/price`

```
// /app/components/price.tsx file

<a href="https://0x.org/" target="_blank" rel="noopener noreferrer">
 <Image src={ZeroExLogo} alt="Icon" width={50} height={50} />
</a>
<ConnectButton />
```

## Add custom button

Next, we want to replace our "Dummy Connect Wallet" Button with a Custom ConnectButton. The code is provided for us in RainbowKit's docs [here](https://www.rainbowkit.com/docs/custom-connect-button). We've made some slight modifications to the button name and styling; otherwise, it is the same.

```
// Replace this Dummy Button code

<button className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded w-full">
 Dummy Connect Wallet
</button>
```

```
// With this ConnectButton.Custom code
<ConnectButton.Custom>
                {({
                  account,
                  chain,
                  openAccountModal,
                  openChainModal,
                  openConnectModal,
                  mounted,
                }) => {
                  const ready = mounted;
                  const connected = ready && account && chain;

                  return (
                    <div
                      {...(!ready && {
                        "aria-hidden": true,
                        style: {
                          opacity: 0,
                          pointerEvents: "none",
                          userSelect: "none",
                        },
                      })}
                    >
                      {(() => {
                        if (!connected) {
                          return (
                            <button
                              className="w-full bg-blue-600 text-white font-semibold p-2 rounded hover:bg-blue-700"
                              onClick={openConnectModal}
                              type="button"
                            >
                              Connect Wallet
                            </button>
                          );
                        }

                        if (chain.unsupported) {
                          return (
                            <button onClick={openChainModal} type="button">
                              Wrong network
                            </button>
                          );
                        }

                        return (
                          <div style={{ display: "flex", gap: 12 }}>
                            <button
                              onClick={openChainModal}
                              style={{ display: "flex", alignItems: "center" }}
                              type="button"
                            >
                              {chain.hasIcon && (
                                <div
                                  style={{
                                    background: chain.iconBackground,
                                    width: 12,
                                    height: 12,
                                    borderRadius: 999,
                                    overflow: "hidden",
                                    marginRight: 4,
                                  }}
                                >
                                  {chain.iconUrl && (
                                    <Image
                                      src={chain.iconUrl}
                                      alt={chain.name ?? "Chain icon"}
                                      width={12}
                                      height={12}
                                      layout="fixed"
                                    />
                                  )}
                                </div>
                              )}
                              {chain.name}
                            </button>

                            <button onClick={openAccountModal} type="button">
                              {account.displayName}
                              {account.displayBalance
                                ? ` (${account.displayBalance})`
                                : ""}
                            </button>
                          </div>
                        );
                      })()}
                    </div>
                  );
                }}
              </ConnectButton.Custom>
```

## Recap

Now we should have a swap widget with two buttons for users to connect their wallets, powered by RainbowKit! Play around with these buttons, connect your wallet and switch chains.

<img width="420" alt="Screenshot 2024-04-19 at 2 05 32 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/38de042b-57b4-4419-b033-ddef26104b45">


See the completed code here
```
https://github.com/0xProject/token-swap-dapp-course-code/tree/main/L2
```

