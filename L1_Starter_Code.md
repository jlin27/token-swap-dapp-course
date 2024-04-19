# Starter Code

The first thing you'll need to do is to clone the repo and go into the L1 folder. We will be building on this starter UI to add in token trading functionality.

```
git clone https://github.com/0xProject/token-swap-dapp-course-code/tree/main
cd L1
```

To run the project, first install the project dependencies

```
npm install
```

Then, start the Next.js development server

```
npm run dev
```

4. Navigate to [http://localhost:3000](http://localhost:3000/)

```
open http://localhost:3000
```

## UI Walk-through

You should see the following UI

<img width="392" alt="Screenshot 2024-04-19 at 2 03 50 PM" src="https://github.com/jlin27/token-swap-dapp-course/assets/8042156/a87b1324-7444-4af1-92f6-8fc442d0bbeb">


A couple key features to call out:

1.  **Connect Wallet button** - Currently this is a dummy button. When this app is complete, clicking this button will enable the user to connect to their wallet, switch chains if needed, and enable the "Swap" button. This wallet connection is powered by RainbowKit.

2.  **Token selectors** - Currently the two token selectors for the sell and buy tokens default to WMATIC and DAI. We will learn how to add more tokens to these lists.

3.  **Amount** - These two amount inputs allow users to input the amount of tokens they would like to sell and buy.

4.  **Swap Button** - As mentioned above, the "Swap" button is currently disabled, but we will enable it when the user has signed into MetaMask.

## Key Files

This code is set up with [Next.js App Router](https://nextjs.org/docs), so the app infrastructure will follow that paradigm. Take a look around the files, specifically paying attention to these:

- `app/components/price.tsx` - This file contains the PriceView which contains the swap widget where the user can select sell/buy tokens, input amounts, and connect their wallet

- `app/providers.tsx` - Contains providers that we will need to run RainbowKit's wallet connect. We will cover this more in the next lesson, so don't worry about it too much now. Read more about it here: https://www.rainbowkit.com/docs/installation

- `app/page.tsx` - This file contains the UI that will be displayed. For now, we just have the PageView.

- `app/layout.tsx`This file is used to render the initial layout of your application, and it can be used to add global functionality to your application.

- `src/constants.ts` - List of constants for the sell and buy token dropdown lists

