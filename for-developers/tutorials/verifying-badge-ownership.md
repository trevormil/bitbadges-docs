# Verifying Badge Ownership

There are many ways to verify if a user owns a badge or not. Select your preferred method according to your use case.

## Common Use Cases

**Blockin**

Check out Blockin which is a universal, multi-chain sign-in standard for Web 3.0 which extends Sign-In with Ethereum for native BitBadges ownership verification. It is a TypeScript library which allows you to a) authenticate users (via a wallet cryptographic signature from any supported chain's wallet) and b) verify a user's ownership of assets (on any chain including BitBadges)!

Blockin can be used for badge-gating websites and more!

**Offline Verification**

For performing offline-first verification (no access to Internet), you can obtain a snapshot of the balances via your preferred method (when you do have Internet).

Then in an offline setting, you can simply:

1. Authenticate user knows the private key for an address via Blockin (or any signature). This is a simple cryptographic signature and doesn't require Internet.
2. Check your snapshot to see if the address owns the badge.



**Ecosystem Tools**

Also, be sure to check out the [Ecosystem](../../overview/ecosystem.md) tools.



## Step 1: Fetch Balances

**BitBadges Website**

If you have access to the website, just simply navigate to the badge page or user's portfolio page. Note that you can check how up to date the information is via the block number and timestamp.

**BitBadges API**

Use one of the following routes to fetch balances from the API (supports off-chain balances but note they use a cache and trigger refresh system). Note these requests are paginated, so you may have to fetch multiple times specifying the previously returned bookmark.

<pre class="language-typescript"><code class="lang-typescript"><strong>POST /api/v0/collection/:collectionId/:badgeId/owners
</strong></code></pre>

```typescript
POST /api/v0/collection/:collectionId
```

<pre class="language-typescript"><code class="lang-typescript"><strong>POST /api/v0/collection/batch
</strong></code></pre>



Note that you can check how up to date the information is by using the **POST /api/v0/status**.

**BitBadges REST Node**

Use this [endpoint](https://bitbadges.github.io/bitbadges-openapi-rest-docs/#bitbadgesBitbadgeschainBadgesGetBalance) to query badge balances directly from a blockchain node (doesn't apply to off-chain balances). Note cosmos addresses must be used.

**Direct Fetch - Off-Chain Balances**

For off-chain balances, you can also directly fetch the balances map from the source URI.

**Run Own Node / Indexer**

You can also always run and fetch balances from your own node / indexer, so you aren't relying on anyone else's infrastructure!

## Step 2: Verify&#x20;

Verification is two-fold. First, you need to verify the user owns the address (knows the private key).  Second, you need to verify ownership of the badge according to your criteria.

See some tools to help you do so below:

**Blockin**

Check a user actually owns their address by requesting and verifying a Blockin signature.

#### **BitBadges SDK**

If you need to programmatically verify balances, check out the BitBadges SDK. Head [here](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/modules.html) and check out the Functions - Balances section.
