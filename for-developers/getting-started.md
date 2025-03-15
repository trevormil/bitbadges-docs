# 🚴‍♂️ Getting Started

> **Need help?** Join our Discord for support from the BitBadges team and community developers.
>
> **Need $BADGE credits?** Contact us on Discord - we offer subsidized credits for developers during beta!
>
> **No-Code / In-Site Solutions** Check out the [Create tab](https://bitbadges.io/create) or the [developer portal](https://bitbadges.io/developer) first to see what all is possible. Most of the time, you can just do everything with no code directly in-site!
>
> **Video Tutorial Series** Check out our three intro to development video tutorial courses ([Intro to BitBadges](https://www.udemy.com/course/multichain-dapps/learn/), [The BitBadges Token Standard](https://www.udemy.com/course/multichain/learn/lecture/46271653#overview), and [Developing MultiChain Applications](https://www.udemy.com/course/crosschain-dapps/learn/lecture/46271733#overview)).

## Gate Any Service with Any Criteria In 2 Steps

Want to build a gated service? Any service can be gated simply in just 2 steps.

1. Authenticate your user. We recommend Sign In with BitBadges, but this can be however you want to.
2. Verify your authenticated user meets the criteria for a claim. Claims are the universal connector of everything. You can set up claims to check any criteria including payments, badge ownership, points, other claims, anything.

```typescript
// By address
const res = await BitBadgesApi.checkClaimSuccess(claimId, address);
// if (res.successCount >= 1) ...

// By attempt ID
const res = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);
// if (res.success) ...

// You might also do so via post-success webhooks or another approach
// This might allow you to map emails -> addresses, for example, if you authenticate via email
```

## Quick Start Guide

1. Install the SDK and use the API:

```bash
npm i bitbadgesjs-sdk
```

```ts
import { BitBadgesAPI } from 'bitbadgesjs-sdk';

const api = new BitBadgesAPI({
  ...YOUR_CONFIG
});

await BitBadgesAPI.getCollections(...);
```

2. Try our interactive quickstart demo: [BitBadges Quickstart](https://bitbadges.io/quickstart). If that is what you need, clone it: [BitBadges Quickstart Repository](https://github.com/BitBadges/bitbadges-quickstart)
   * Includes authentication, transactions, self-hosting, and API examples
   * Multiple flavors available (e.g., Tailwind CSS)
   * Open for community contributions!

<figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

**Additional Resources:**

* [Auth.js/Next.js Template](https://github.com/BitBadges/bitbadges-authjs-example)

## Development Paths

### 1. Creating Badges, Lists, Claims, & Attestations

* **Get Started**: Use the Create tab on BitBadges web app
* **Self-Hosted / Advanced Solutions**:
  * Host your own off-chain balances or metadata
  * Control off-chain badge allocation via your server
  * Integrate with Web2 data (e.g., subscription status)
  * Learn more about [balance types](badges-advanced/balances-transfers/balance-types.md)
  * Test and manage claims in the [developer portal](https://bitbadges.io/developer)
  * Build custom claim plugins

{% content-ref url="../overview/claim-builder/" %}
[claim-builder](../overview/claim-builder/)
{% endcontent-ref %}

### 2. Creating a Custom App / Use Case?

* Use [BitBadges API](bitbadges-api/api.md) and [SDK](bitbadges-sdk/) for data queries
* Use Sign In with BitBadges for multi-chain authentication / API scope authorizations
  * One interface, all chains!
* Create custom claims and check any criteria you want behind the scenes seamlessly!
  * Claim checks which means you can check criteria like badge ownership or anything from 7000+ integrations
  * Directly receive private attestation data or other sign in data
* Run custom indexers for specialized data needs

{% content-ref url="authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](authenticating-with-bitbadges/)
{% endcontent-ref %}

### 3. Integration Options

* **Integrate BitBadges Into Your App**: Use our API/SDK
* **Integrate Your Tool Into BitBadges**:
  * Create custom claim plugins
  * Create badges, listings, address lists, anything else
  * Contact us for native integration options

### 4. Blockchain

**Transaction Management Options:**

* **Recommended**: Use the BitBadges web app Create tab
* **Alternative Methods**:
  1. Use our [helper in-site broadcast tool](bitbadges-blockchain/create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md). This even supports redirects with custom inputs!
  2. Generate transactions via [SDK](bitbadges-blockchain/create-and-broadcast-txs/)
  3. Use blockchain node CLI (BitBadges addresses only)

**Other Options:**

* [Run a Node](bitbadges-blockchain/run-a-node/) for direct blockchain interaction
* Create [CosmWasm smart contracts](bitbadges-blockchain/create-a-wasm-contract.md)
* Extend functionality with BitBadges SDK
