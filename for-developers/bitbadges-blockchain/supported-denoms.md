# Supported Denoms

BitBadges implements an allowlist for supported denominations that you can use within the token standard on its chain.

## The two USDC routes

USDC appears twice, and the difference matters when you write code against it.

An IBC denom is the hash of the token's full transfer path, so the same
underlying asset arriving by two different routes has two different denoms:

| Symbol       | Route                                                                  | Denom             |
| ------------ | ---------------------------------------------------------------------- | ----------------- |
| `USDC`       | `transfer/channel-40/erc20:0xa00C59fF5a080D2b954d0c75e46E22a0c371235a` | `ibc/E1116484...` |
| `USDC.n` | `transfer/channel-2/uusdc`                                             | `ibc/F082B65C...` |

Canonical `USDC` is [Circle's **native** USDC on Injective](https://injective.com/usdc)
(erc20 contract `0xa00C59fF5a080D2b954d0c75e46E22a0c371235a`, CCTP-enabled),
sent **one IBC hop** from Injective to BitBadges over `channel-40` — not a
Noble voucher forwarded through Injective. The erc20 address in the trace is
checksummed; the denom hash is case-sensitive.

{% hint style="warning" %}
**Canonical `USDC` is live but early.** The route is proven — native USDC
bridged from Injective over `channel-40` mints `ibc/E1116484...` exactly — and
its token-standard allowlisting ships with governance proposal 45 (an
expedited `tokenization` params update). Circulating supply is still small.
Skip Go now indexes the canonical denom on `bitbadges-1` automatically (Skip's
own data labels it `USDC.inj`; the BitBadges app shows it as `USDC`) and can
route native Injective USDC to BitBadges as a direct transfer — so if you
hold USDC on Injective, acquisition is a Skip-routed transfer away. Broader
routes (starting from Noble or Ethereum USDC) are still pending Skip
swap-venue coverage and pool liquidity; until then, get to native USDC on
Injective first (via CCTP or a swap there).
{% endhint %}

`USDC` — Circle's native USDC on Injective, one hop away — is the canonical
denom. Use it for everything new: pricing, payment requests, subscriptions,
prediction markets, pool creation, backed collections. From
`bitbadges@0.43.0`, the bare symbol `USDC` — in the SDK registry, in
`bb --denom USDC`, in `"denom": "USDC"` JSON — resolves to
`ibc/E1116484...`. Existing Noble-USDC holders who want the canonical asset
convert to native USDC on Injective (via CCTP or a swap there) and send it
over.

## Legacy backwards compatibility: `USDC.n`

`USDC.n` is the original Noble-direct denom. It exists purely as temporary
backwards compatibility for existing balances and collections — **do not use
it for anything new**. The `.n` suffix is Skip Go's ecosystem-wide symbol for
the Noble voucher (Skip's `bitbadges-1` registry already uses `USDC.n`);
earlier drafts spelled it `USDC.noble`, and as of `bitbadges@0.43.0` the CLI,
SDK builders, and MCP server still accept typed input `USDC.noble` as an alias
(display and output are always `USDC.n`).

- **Existing balances remain fully usable and spendable, forever.** They stay
  priced at $1 and stay Skip-supported so holders can swap out.
- **`channel-2` stays open indefinitely.** The Noble-direct route is not being
  closed, and no decommissioning is planned.
- **The 16 collections with backed paths on it keep working forever.** A
  backed path's escrow address is derived from the denom string itself, so
  those collections cannot be repointed and stay on `USDC.n` permanently.
  This is also why creating a **new** backed collection on `USDC.n` is
  actively harmful: it would be stuck there permanently too. New backed
  collections use canonical `USDC`.
- **Deprecated for everything new.** New pricing, payment requests,
  subscriptions, pools, and backed collections use canonical `USDC`.

If you are handling balances generically, treat the two as separate denoms with
separate balances — they do not aggregate.

Other USDC variants — e.g. Osmosis's alloyed `allUSDC` or any bridged voucher
of it — are **unregistered and unsupported on BitBadges**: they arrive as raw
`ibc/` denoms, are not allowlisted in `x/tokenization`, carry no rate limits,
and cannot back collections. The supported routes remain exactly canonical
`USDC` (via Injective) and legacy `USDC.n`.

## Registry

{% hint style="info" %}
**Ships in `bitbadges` 0.43.0.** The `deprecated` and `deprecationNote` fields
on `CoinDetails`, and resolving the bare symbol `USDC` to the canonical denom,
land in **`bitbadges@0.43.0`**
([`bitbadgesjs` #273](https://github.com/BitBadges/bitbadgesjs/pull/273)).
`0.43.0` is **not yet published to npm** — the latest release is `0.42.x`,
which does not have them. If you are pinned below `0.43.0`, the snippet below
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
  // Canonical USDC: Circle's native USDC on Injective, one IBC hop away.
  'ibc/E1116484B327AEE59CDC3DA73D319834781A13DB2A7DFC1F38A30CD45ABF58B8': {
    skipGoSupported: true,
    label: 'USDC',
    symbol: 'USDC',
    decimals: '6',
    baseDenom: 'ibc/E1116484B327AEE59CDC3DA73D319834781A13DB2A7DFC1F38A30CD45ABF58B8',
    image: 'https://github.com/cosmos/chain-registry/blob/master/noble/images/USDCoin.png?raw=true'
  },
  // Legacy Noble-direct USDC. Still supported; still Skip-supported so that
  // converting *out* of it will work once the canonical route is liquid.
  'ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349': {
    skipGoSupported: true,
    label: 'USDC.n',
    symbol: 'USDC.n',
    decimals: '6',
    baseDenom: 'ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349',
    image: 'https://github.com/cosmos/chain-registry/blob/master/noble/images/USDCoin.png?raw=true',
    deprecated: true,
    deprecationNote: 'Legacy Noble-routed USDC. Existing balances stay fully usable — use canonical USDC (via Injective) for everything new.'
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
