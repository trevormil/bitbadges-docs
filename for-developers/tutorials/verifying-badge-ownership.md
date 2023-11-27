# Verifying Badge Ownership

There are many ways to verify if a user owns a badge or not. Select your preferred method according to your use case and requirements.

Remember, verification is two-fold. First, you need to verify the user owns the address.  Second, you need to verify ownership of the badge according to your criteria.



## Step 1: Verify Balances

Keep in mind the potential delay / lag and availability with the options below. For example, the BitBadges API / website tries to process blocks as fast as possible and is up to date with the blockchain most of the time; however, it may fall behind in some cases.

**BitBadges Website**

If you have access to the website, just simply navigate to the badge page or user's portfolio page.&#x20;

**BitBadges API**

Use one of the following routes to fetch balances from the [API](../bitbadges-api/api.md) (supports off-chain balances but note they use a cache and trigger refresh system). These requests are paginated, so you may have to fetch multiple times specifying the previously returned bookmark.

<pre class="language-typescript"><code class="lang-typescript"><strong>POST /api/v0/collection/:collectionId/:badgeId/owners
</strong></code></pre>

```typescript
POST /api/v0/collection/:collectionId
```

<pre class="language-typescript"><code class="lang-typescript"><strong>POST /api/v0/collection/batch
</strong></code></pre>

Note that you can check how up to date the information is by using the **POST /api/v0/status**.

You can also run your own indexer / API!

**BitBadges Blockchain Node - REST API**

Use this [endpoint](https://bitbadges.github.io/bitbadges-openapi-rest-docs/#bitbadgesBitbadgeschainBadgesGetBalance) to query badge balances directly from a blockchain node (doesn't apply to off-chain balances). Cosmos addresses must be used.

You can also always run and fetch balances from your own node / indexer, so you aren't relying on anyone else's infrastructure!

**Direct Fetch - Off-Chain Balances**

For collections with off-chain balances, you can also directly fetch the balances map from the source URI.

## Step 2: Verify Address Ownership

Remember, verification is two-fold. You need to verify the user owns the address and verify ownership of the badge according to your criteria.&#x20;

Address ownership can be verified with a cryptographic signature.

## Tools and Common Use Cases

**Multi-Chain Web3 Authentication w/ BitBadges Support - Blockin**

Check out [Blockin](https://blockin-quickstart.vercel.app) which is a universal, multi-chain sign-in standard for Web 3.0 which extends Sign-In with Ethereum for native BitBadges ownership verification. It is a TypeScript library which allows you to a) authenticate users (via a wallet cryptographic signature from any supported chain's wallet) and b) verify a user's ownership of assets (on multiple L1 chains including BitBadges)!

Blockin can be used for badge-gating websites and more!

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**Offline Verification**

For performing offline-first verification (no access to Internet), you can obtain a snapshot of the balances via your preferred method (when you do have Internet).

Then in an offline setting, you can simply:

1. Authenticate user knows the private key for an address via Blockin (or any signature). This is a simple cryptographic signature and doesn't require Internet.
2. Check your snapshot to see if the address owns the badge. This can be done manually or with a tool like Blockin.

**Ecosystem Tools**

Also, be sure to check out the [Ecosystem](../../overview/ecosystem.md) tools.
