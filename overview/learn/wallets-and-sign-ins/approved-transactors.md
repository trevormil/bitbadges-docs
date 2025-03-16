# Approved Transactors

**Approved Transactors**

In your account settings, BitBadges provides the option to approve other wallets to transact on-chain your behalf. Note this is different from the alternate sign ins on the prior page which approve other ways to access your BitBadges account (off-chain). This feature can be used to configure hot wallets, for example, that are easier to access and can sign on your behalf on the go.

Note: This feature is opt-in and not enabled by default.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**BitBadges Embedded Wallet**

While we allow approving any wallet / address, we offer a managed embedded wallet maintained by BitBadges. We will sign transactions / messages when requested, but the requester has to be signed in with the Embedded Wallet (embeddedWallet) scope enabled. This can be approved with an alternate sign-in method saved for your account, or check out the Sign In with BitBadges documentaion if you want to do this programmatically.

Note: The embedded wallet is its own address and needs to be treated as such. It needs to be registered, be funded, etc.

To sign transactions:

1. You must be signed in with permission to use the embedded wallet (the Embedded Wallet scope). You can configure alternate sign in methods (socials or other addresses) via the Sign In Methods tab. Ensure the Embedded Wallet scope is enabled.
2. The desired transaction message types must be approved in the account settings before transacting.
3. The wallet must have sufficient $BADGE credits.

Note this is opt-in and can be revoked at any time. This is provided as a way to trade off centralization / trust for enhanced user experience but is completely optional. Also note that the BitBadges embedded wallet is not the only option. We provide the functionality for you to approve any wallet.
