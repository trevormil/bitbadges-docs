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

{% hint style="warning" %}
**`USDC` is not liquid yet.** No USDC has been bridged over the
Injective route to date — `channel-40` has never carried a transfer packet, and
the canonical denom's on-chain supply is currently **zero**. Nobody can pay you
in it today.

If you price a collection, payment request, or subscription in `ibc/0E485657...`
right now, no buyer will be able to settle it. Until bridging is live, price in
`USDC.noble` (`ibc/F082B65C...`) or `ubadge`, which have real circulating
supply.

Watch the bare symbol. From `bitbadges@0.43.0` on, the symbol `USDC` — in the
SDK registry, in `bb --denom USDC`, in `"denom": "USDC"` JSON — resolves to the
canonical `ibc/0E485657...`, not to the liquid Noble route. Spell it
`USDC.noble` (or pass `ibc/F082B65C...`) when you mean the denom people
actually hold today.

Bridging instructions for the Injective route are pending and will be published
on this page. Treat the guidance below as the *target* state to build toward,
not as something usable today.
{% endhint %}

`USDC` — routed through Injective — is the canonical denom going forward. Once
it is liquid, it is the denom to use for anything new: pricing, payment
requests, subscriptions, prediction markets, pool creation.

`USDC.noble` is the original Noble-direct route, and it is where all real USDC
liquidity on BitBadges lives today. To be precise about what "deprecated" means
here:

- **`USDC.noble` is deprecated for new integrations.** New code should target
  the canonical `USDC` denom once bridging is available.
- **`channel-2` stays open indefinitely.** The Noble-direct route is not being
  closed, and no decommissioning is planned.
- **Existing balances remain fully usable and spendable.** They are still
  priced at $1 and stay Skip-supported. There is nothing to migrate today —
  the canonical route has no liquidity yet, so no swap out of `USDC.noble`
  into `USDC` is possible. Skip support stays on so that swap works the day
  the Injective route goes live.
- **Collections backed by it cannot move.** A backed path's escrow address is
  derived from the denom string itself, so a collection that declared a backed
  path against `USDC.noble` cannot be repointed without stranding its escrow.
  Those collections stay on `USDC.noble` permanently.

If you are handling balances generically, treat the two as separate denoms with
separate balances — they do not aggregate. If you are picking a denom for the
user today, pick `USDC.noble`; revisit once the canonical route is liquid.

## Registry

{% hint style="info" %}
**Ships in `bitbadges` 0.43.0.** The `deprecated` and `deprecationNote` fields
on `CoinDetails`, and resolving the bare symbol `USDC` to the canonical denom,
land in **`bitbadges@0.43.0`**
([`bitbadgesjs` #273](https://github.com/BitBadges/bitbadgesjs/pull/273)). They
are not present in `0.42.x`. If you are pinned below `0.43.0`, the snippet below
will not typecheck — drop those two fields.
{% endhint %}

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
  // Legacy Noble-direct USDC. Still supported; still Skip-supported so that
  // converting *out* of it will work once the canonical route is liquid.
  'ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349': {
    skipGoSupported: true,
    label: 'USDC.noble',
    symbol: 'USDC.noble',
    decimals: '6',
    baseDenom: 'ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349',
    image: 'https://github.com/cosmos/chain-registry/blob/master/noble/images/USDCoin.png?raw=true',
    deprecated: true,
    deprecationNote: 'Legacy Noble-routed USDC. Fully usable and staying — new integrations should target canonical USDC (via Injective) once that route is live.'
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
