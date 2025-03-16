# 🧪 Testnet Mode

Testnet mode provides a separate environment for testing purposes. Simply turn on the switch (or go to [testnet.bitbadges.io](https://testnet.bitbadges.io)). It is isolated from the production environment and uses its own resources, such as a testnet blockchain, database, and more.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Important Notes

You should treat testnet as an entirely SEPARATE service. NOTHING will carry over from normal mode (not even profiles, badges, settings, anything).  Consider this before determining whether testnet mode is the correct option for you.&#x20;

<pre><code><strong>Note: Third-party integrations (e.g. claim plugins) are the exact same.
</strong></code></pre>

### Differences

* Some features available in production may not be accessible in testnet:
  * Off-chain balances managed by BitBadges are not hosted externally (via CDN)
  * Buying $BADGE credits
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

* RPC: [https://testnet.node.bitbadges.io/rpc](https://testnet.node.bitbadges.io/rpc)
* REST: [https://testnet.bitbadges.io/api](https://testnet.bitbadges.io/api)
* Direct Node Access: `http://138.197.10.8:YOUR_PORT`

### Feedback

If there is anything we can do to make development easier, let us know.
