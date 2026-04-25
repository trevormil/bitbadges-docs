# React & Next.js Quickstart

{% hint style="warning" %}
**Testnet is temporarily offline** (as of 2026-04-25) to reduce hosting costs while it sees minimal usage. Mainnet ([https://bitbadges.io](https://bitbadges.io)) is the only currently live network. The testnet examples (`bitbadges-2`, EVM chain `50025`) shown on this page are retained for reference and will become accurate again when testnet is reactivated — for now, swap `'testnet'` for `'mainnet'` and `bitbadges-2` for `bitbadges-1`.
{% endhint %}

Wire BitBadges into a scratch React or Next.js app in under 10 minutes. This page walks through the canonical frontend flow: **install → connect wallet → query a collection → sign + broadcast a transaction** as one copy-pasteable set of snippets.

> **Framing this page assumes:** Next.js 14+ with the **App Router**, **Keplr** as the featured wallet (BitBadges is a Cosmos chain, so Keplr is the native choice), and the **testnet** (`bitbadges-2`, EVM chain `50025`) for examples. Plain React (Vite/CRA) works identically — only the `"use client"` directive is Next.js-specific. Pages Router users should add the same snippets to a component rendered inside `_app.tsx`.
>
> MetaMask is also fully supported via the EVM path — see the [EVM wallet section](#connect-wallet-metamask--evm-path) below.

## 1. Install

```bash
npm install bitbadges ethers
```

`ethers` is only required if you plan to use MetaMask or any EVM wallet. Keplr-only apps can skip it.

## 2. Connect Wallet (Keplr — default)

Keplr is the Cosmos-native wallet and the recommended path for BitBadges. Users install the Keplr browser extension once, then your app calls `GenericCosmosAdapter.fromKeplr(chainId)` to get a wallet adapter.

```tsx
// app/components/ConnectKeplr.tsx
'use client';

import { useEffect, useState } from 'react';
import { GenericCosmosAdapter, type WalletAdapter } from 'bitbadges';

export function ConnectKeplr() {
  const [adapter, setAdapter] = useState<WalletAdapter | null>(null);
  const [address, setAddress] = useState<string>('');
  const [error, setError] = useState<string>('');

  async function connect() {
    try {
      // 'bitbadges-2' = testnet. Use 'bitbadges-1' for mainnet.
      const a = await GenericCosmosAdapter.fromKeplr('bitbadges-2');
      setAdapter(a);
      setAddress(a.address);
    } catch (e: any) {
      setError(e.message ?? 'Failed to connect Keplr');
    }
  }

  return (
    <div>
      {address ? (
        <p>Connected: {address}</p>
      ) : (
        <button onClick={connect}>Connect Keplr</button>
      )}
      {error && <p style={{ color: 'red' }}>{error}</p>}
    </div>
  );
}
```

**Note:** If the user does not have Keplr installed, `fromKeplr` throws. Detect with `typeof window !== 'undefined' && (window as any).keplr` and prompt the user to install from [keplr.app](https://www.keplr.app/).

## 3. Connect Wallet (MetaMask — EVM path)

BitBadges also supports any EVM wallet (MetaMask, Rabby, Coinbase Wallet) via precompiles. The SDK routes transactions through the EVM JSON-RPC automatically when you use `GenericEvmAdapter`.

```tsx
// app/components/ConnectMetaMask.tsx
'use client';

import { useState } from 'react';
import {
  GenericEvmAdapter,
  NETWORK_CONFIGS,
  type WalletAdapter,
} from 'bitbadges';

export function ConnectMetaMask() {
  const [adapter, setAdapter] = useState<WalletAdapter | null>(null);
  const [address, setAddress] = useState<string>('');

  async function connect() {
    const a = await GenericEvmAdapter.fromBrowserWallet({
      // Throws a clear error if the user is on the wrong network.
      expectedChainId: NETWORK_CONFIGS['testnet'].evmChainId, // 50025
    });
    setAdapter(a);
    setAddress(a.address);
  }

  return address ? (
    <p>Connected: {address}</p>
  ) : (
    <button onClick={connect}>Connect MetaMask</button>
  );
}
```

> **Cosmos vs EVM addresses:** The same user gets a **different address** depending on which adapter they use (Cosmos derivation vs Ethereum derivation). Decide up front which path your app supports, and fund the right address. See [Signing Client — Server-Side](./../bitbadges-blockchain/create-and-broadcast-txs/signing-client.md#server-side-signing-evm-path--recommended) for details.

## 4. Query a Collection

The `BitBadgesAPI` class is wallet-independent — you can instantiate it as soon as the page loads and query public data without a wallet connection.

```tsx
// app/components/CollectionInfo.tsx
'use client';

import { useEffect, useState } from 'react';
import { BitBadgesAPI, BigIntify } from 'bitbadges';

const api = new BitBadgesAPI({
  convertFunction: BigIntify,
  apiKey: process.env.NEXT_PUBLIC_BITBADGES_API_KEY, // get one at bitbadges.io/developer
});

export function CollectionInfo({ collectionId }: { collectionId: string }) {
  const [name, setName] = useState<string>('');

  useEffect(() => {
    api.getCollection(collectionId).then((res) => {
      setName(res.cachedCollectionMetadata?.name ?? 'Untitled');
    });
  }, [collectionId]);

  return <p>Collection #{collectionId}: {name}</p>;
}
```

**API key on the client:** `NEXT_PUBLIC_*` env vars are exposed to the browser. That is fine for **read-only** keys from `bitbadges.io/developer`. Keep write keys server-side only — call your own API route instead of hitting BitBadges from the browser.

## 5. Sign + Broadcast a Transaction

Once you have an adapter (from step 2 or 3), create a `BitBadgesSigningClient` and send messages. The client auto-estimates gas, handles sequence numbers, and retries on mismatch.

```tsx
// app/components/TransferButton.tsx
'use client';

import { useState } from 'react';
import {
  BitBadgesSigningClient,
  MsgTransferTokens,
  type WalletAdapter,
} from 'bitbadges';

export function TransferButton({ adapter }: { adapter: WalletAdapter }) {
  const [txHash, setTxHash] = useState<string>('');

  async function send() {
    const client = new BitBadgesSigningClient({
      adapter,
      network: 'testnet', // omit or set to 'mainnet' for production
    });

    const msg = MsgTransferTokens.create({
      creator: client.address,
      collectionId: '1',
      transfers: [
        {
          from: client.address,
          toAddresses: ['bb1...'], // recipient
          balances: [
            {
              amount: '1',
              tokenIds: [{ start: '1', end: '1' }],
              ownershipTimes: [{ start: '1', end: '18446744073709551615' }], // forever
            },
          ],
        },
      ],
    });

    const result = await client.signAndBroadcast([msg]);
    if (result.success) {
      setTxHash(result.txHash);
    } else {
      console.error('Failed:', result.error);
    }
  }

  return (
    <>
      <button onClick={send}>Transfer</button>
      {txHash && <p>Tx: {txHash}</p>}
    </>
  );
}
```

For write flows that only need a signature (not a full transaction — e.g. [Sign In with BitBadges](./../sign-in-with-bitbadges/README.md)), skip `BitBadgesSigningClient` and call `adapter.signArbitrary(...)` directly.

## Putting It Together

A minimal `app/page.tsx` that wires the above components:

```tsx
'use client';

import { useState } from 'react';
import type { WalletAdapter } from 'bitbadges';
import { GenericCosmosAdapter } from 'bitbadges';
import { CollectionInfo } from './components/CollectionInfo';
import { TransferButton } from './components/TransferButton';

export default function Home() {
  const [adapter, setAdapter] = useState<WalletAdapter | null>(null);

  async function connect() {
    setAdapter(await GenericCosmosAdapter.fromKeplr('bitbadges-2'));
  }

  return (
    <main>
      <h1>My BitBadges App</h1>
      <CollectionInfo collectionId="1" />
      {adapter ? (
        <>
          <p>Connected: {adapter.address}</p>
          <TransferButton adapter={adapter} />
        </>
      ) : (
        <button onClick={connect}>Connect Keplr</button>
      )}
    </main>
  );
}
```

## Reference Implementation

The [bitbadges-frontend](https://github.com/BitBadges/bitbadges-frontend) repo is a production Next.js app using these exact primitives. Good for seeing how wallet state is lifted into React context, how network switching is handled, and how transaction UX (approve / reject / pending) is wired.

## Next Steps

- **[Signing Client Reference](./../bitbadges-blockchain/create-and-broadcast-txs/signing-client.md)** — every `BitBadgesSigningClient` option, network preset, and error path.
- **[Common Snippets](./common-snippets/README.md)** — balance lookups, metadata, transfers with increments, approval inspection.
- **[Sign In with BitBadges](./../sign-in-with-bitbadges/README.md)** — OAuth-style authentication if you don't need transactions, just wallet identity.
- **[SDK Types](./sdk-types.md)** — `NumberType` conventions, `BigIntify` / `Stringify`, the 3D balance array.
- **[Testnet Faucet](./../ai-agents/testnet-faucet.md)** — grab BADGE on testnet (`bitbadges-2`) to test the transfer flow.
