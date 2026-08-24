# Supported Denoms

BitBadges implements an allowlist for supported denominations that you can use within the token standard on its chain.

## The two USDC routes

USDC appears twice, and the difference matters when you write code against it.

An IBC denom is the hash of the token's full transfer path, so the same
underlying asset arriving by two different routes has two different denoms:

| Symbol       | Route                                           | Denom             |
| ------------ | ----------------------------------------------- | ----------------- |
| `USDC`       | `transfer/channel-40/transfer/channel-148/uusdc` | `ibc/0E485657...` |
| `USDC.noble` | `transfer/channel-2/uusdc`                       | `ibc/F082B65C...` |

`USDC` — routed through Injective — is canonical. Use it for anything new:
pricing, payment requests, subscriptions, prediction markets, pool creation.
Resolving the bare symbol `USDC` through the SDK returns this denom.

`USDC.noble` is the original Noble-direct route. It is **not** being retired.
Balances in it remain fully spendable, it is still priced at $1, and it stays
Skip-supported precisely so holders can swap out of it. Some collections declare
a backed path against it — a backed path's escrow address is derived from the
denom string itself, so those collections cannot be repointed and will continue
to use it.

If you are handling balances generically, treat the two as separate denoms with
separate balances. If you are picking a denom for the user, pick `USDC`.

## Registry

```typescript
export const MAINNET_COINS_REGISTRY: Record<string, CoinDetails> = {
  ubadge: {
    skipGoSupported: true,
    label: 'BADGE',
    symbol: 'BADGE',
    decimals: '9',
    baseDenom: 'ubadge',
    image: 'https://github.com/cosmos/chain-registry/blob/master/bitbadges/images/badge_logo.png?raw=true'
  },
  'badges:49:chaosnet': {
    skipGoSupported: false,
    label: 'CHAOS',
    symbol: 'CHAOS',
    decimals: '9',
    baseDenom: 'badges:49:chaosnet',
    image: 'https://bitbadges.io/_next/image?url=https%3A%2F%2Fipfs.bitbadges.io%2Fipfs%2FQmdRQUvQBo6p24RQ7AS7RD6srqyUjoHJ5Cjs4p22zie9bQ&w=1920&q=75'
  },
  // Canonical USDC, routed through Injective.
  'ibc/0E485657AEF4C39D551E7D53463734E4C445A96E6C814DC4C2FF0031470B40BB': {
    skipGoSupported: true,
    label: 'USDC',
    symbol: 'USDC',
    decimals: '6',
    baseDenom: 'ibc/0E485657AEF4C39D551E7D53463734E4C445A96E6C814DC4C2FF0031470B40BB',
    image: 'https://github.com/cosmos/chain-registry/blob/master/noble/images/USDCoin.png?raw=true'
  },
  // Legacy Noble-direct USDC. Still supported; still Skip-supported on purpose,
  // since swapping *out* of it is how a holder converts to canonical USDC.
  'ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349': {
    skipGoSupported: true,
    label: 'USDC.noble',
    symbol: 'USDC.noble',
    decimals: '6',
    baseDenom: 'ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349',
    image: 'https://github.com/cosmos/chain-registry/blob/master/noble/images/USDCoin.png?raw=true',
    deprecated: true,
    deprecationNote: 'Legacy Noble-routed USDC. Swap to USDC (via Injective) — balances remain fully usable.'
  },
  'ibc/A4DB47A9D3CF9A068D454513891B526702455D3EF08FB9EB558C561F9DC2B701': {
    skipGoSupported: true,
    label: 'ATOM',
    symbol: 'ATOM',
    decimals: '6',
    baseDenom: 'ibc/A4DB47A9D3CF9A068D454513891B526702455D3EF08FB9EB558C561F9DC2B701',
    image: 'https://github.com/cosmos/chain-registry/blob/master/cosmoshub/images/atom.png?raw=true'
  },
  'ibc/ED07A3391A112B175915CD8FAF43A2DA8E4790EDE12566649D0C2F97716B8518': {
    skipGoSupported: true,
    label: 'OSMO',
    symbol: 'OSMO',
    decimals: '6',
    baseDenom: 'ibc/ED07A3391A112B175915CD8FAF43A2DA8E4790EDE12566649D0C2F97716B8518',
    image: 'https://github.com/cosmos/chain-registry/blob/master/osmosis/images/osmo.png?raw=true'
  }
};
```
