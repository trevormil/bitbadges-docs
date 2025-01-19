# 🚴‍♂️ Getting Started

> **Need help?** Join our Discord for support from the BitBadges team and community developers.
>
> **Need $BADGE credits?** Contact us on Discord - we offer subsidized credits for developers during beta!
>
> **No-Code / In-Site Solutions** Check out the [Create tab](https://bitbadges.io/create) or the [developer portal](https://bitbadges.io/developer) first to see what all is possible. Most of the time, you can just create with no-code directly in-site!

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
    - Includes authentication, transactions, self-hosting, and API examples
    - Multiple flavors available (e.g., Tailwind CSS)
    - Open for community contributions!

<figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

**Additional Resources:**

-   [Auth.js/Next.js Template](https://github.com/BitBadges/bitbadges-authjs-example)

## Development Paths

### 1. Creating Badges, Lists, Claims, & Attestations

-   **Get Started**: Use the Create tab on BitBadges web app
-   **Self-Hosted / Advanced Solutions**:
    -   Host your own off-chain balances or metadata
    -   Control off-chain badge allocation via your server
    -   Integrate with Web2 data (e.g., subscription status)
    -   Learn more about [balance types](core-concepts/balances-transfers/balance-types.md)
    -   Test and manage claims in the [developer portal](https://bitbadges.io/developer)
    -   Build custom claim plugins

{% content-ref url="../overview/claim-builder/" %}
[claim-builder](../overview/claim-builder/)
{% endcontent-ref %}

### 2. Data Access & API Integration

-   Use [BitBadges API](bitbadges-api/api.md) and [SDK](bitbadges-sdk/) for data queries
-   Run custom indexers for specialized data needs

### 3. Sign In with BitBadges

This is the typical flow for most developers creating because everything is accessible all in one place!

-   Multi-chain authentication
-   Claim checks which means you can check criteria like badge ownership or anything from 7000+ integrations
-   Directly receive private attestation data or other sign in data
-   Access BitBadges API scopes on behalf of users

{% content-ref url="authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](authenticating-with-bitbadges/)
{% endcontent-ref %}

### 4. Integration Options

-   **Integrate BitBadges Into Your App**: Use our API/SDK
-   **Integrate Your Tool Into BitBadges**:
    -   Create custom claim plugins
    -   Create badges, listings, address lists, anything else
    -   Contact us for native integration options

### 5. Blockchain-Based Development

**Transaction Management Options:**

-   **Recommended**: Use the BitBadges web app Create tab
-   **Alternative Methods**:
    1. Use our [helper in-site broadcast tool](create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md). This even supports redirects with custom inputs!
    2. Generate transactions via [SDK](create-and-broadcast-txs/)
    3. Use blockchain node CLI (BitBadges addresses only)

**Other Options:**

-   [Run a Node](bitbadges-blockchain/run-a-node/) for direct blockchain interaction
-   Create [CosmWasm smart contracts](bitbadges-blockchain/create-a-wasm-contract.md)
-   Extend functionality with BitBadges SDK
