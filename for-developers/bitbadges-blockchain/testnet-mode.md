# Testnet Mode

{% hint style="warning" %}
**Testnet is temporarily offline** (as of 2026-04-25) to reduce hosting costs while it sees minimal usage. Mainnet ([https://bitbadges.io](https://bitbadges.io)) is the only currently live network. This page is retained for reference and will become accurate again when testnet is reactivated.
{% endhint %}

Testnet mode provides a separate environment for testing purposes. Simply turn on the switch (or go to [testnet.bitbadges.io](https://testnet.bitbadges.io)). It is isolated from the production environment of BitBadges and uses its own resources, such as a testnet blockchain, database, API, and more.

<pre><code><strong>Note: Third-party integrations (e.g. claim plugins) are the exact same. 
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (129) (1).png" alt=""><figcaption></figcaption></figure>

### Important Notes

You should treat testnet as an entirely SEPARATE service. NOTHING will carry over from normal mode (not even profiles, badges, settings, anything). Consider this before determining whether testnet mode is the correct option for you.

### Differences

* Some features available in production may not be accessible in testnet:
  * Off-chain balances managed by BitBadges are not hosted externally (via CDN)
  * Buying BADGE credits
  * Push notifications
  * And more
* Some restrictions may be more relaxed
  * No API keys required
  * Faucet may be more lenient
* Performance also may differ from the production environment

### Links

Frontend: [https://testnet.bitbadges.io](https://testnet.bitbadges.io)

Backend: [https://api.bitbadges.io/testnet](https://api.bitbadges.io/testnet) (append the normal routes to this base URL)

Testnet Node:

**Cosmos SDK Endpoints:**
* RPC: [https://rpc-testnet.bitbadges.io](https://rpc-testnet.bitbadges.io)
* REST: [https://lcd-testnet.bitbadges.io](https://lcd-testnet.bitbadges.io)

**EVM JSON-RPC Endpoints:**
* EVM RPC: [https://evm-rpc-testnet.bitbadges.io](https://evm-rpc-testnet.bitbadges.io)

For detailed information about EVM RPC endpoints, see [EVM RPC Endpoints](evm-rpc-endpoints.md).

### Faucet API

The testnet faucet provides free BADGE tokens for testing and bot development. No API key required.

```
POST https://api.bitbadges.io/testnet/api/v0/faucet
```

See [Testnet Faucet API](../ai-agents/testnet-faucet.md) for full details, examples, and rate limits.

### Feedback

If there is anything we can do to make development easier, let us know.
