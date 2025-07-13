# Overview

Sign In with BitBadges (SIWBB) is a unified multi-chain authentication solution that works across all blockchain ecosystems. It replaces traditional "Sign In with X" buttons and can be configured to handle the following all in one flow:

* Multi-chain authentication
* Verification of attestation signatures
* Badge ownership verification
* Integration with 7000+ supported apps and plugins
* BitBadges API scope authorizations

Outsource the heavy lifting of authentication to us, allowing you to focus on your core utility. We aim to provide maximum flexibility in the design process.

```
NOTE: The QR codes flow has been deprecated.

You may still use Sign In with BitBadges and generate a QR for your user on your own.

We also have the peer-to-peer verification still enabled for on-the-fly QR verification.

Simply replace any QR code flow with your own and use claims, the standard SIWBB, and other tools.
```

#### OAuth Endpoints

* **Authorization:** `https://bitbadges.io/siwbb/authorize?your_params`
* **Token:** `https://api.bitbadges.io/api/v0/siwbb/token`
* **Revoke:** `https://api.bitbadges.io/api/v0/siwbb/token/revoke`&#x20;

[**Demo**](https://bitbadges.io/siwbb/authorize?expectAttestations=true\&client_id=example-client-id\&redirect_uri=https://example.com&)

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

## Implementation Flow

1. **Authentication- BitBadges Side**
   * User accesses BitBadges URL (direct or popup)
   * Proves address ownership and other criteria (badges, attestations, etc.)
   * Receives authorization code from BitBadges (QR or behind the scenes via redirect)
2. **Integration- Your App Side**
   * Receive authentication response
   * Check criteria on your end
   * Implements security measures (prevent replay attacks, etc.)
   * Focus on your core utility

## Getting Started

Check out our [BitBadges quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) for a complete implementation example and reference code.

Note: SIWBB is fully OAuth 2.0 compatible and works with standard OAuth frameworks and tools.

**Hybrid No Wallet dApps**

Sign In with BitBadges is unique because it allows you to build hybrid dApps, as we term them. Hybrid dApps outsource ALL of the wallet connection, signing, authentication to us. You have no need to implement anything wallet-related on your app side if not needed.

However, if your app already uses wallets, you can still use SIWBB.  The flow is the exact same, and as the user navigates to the SIWBB page, the user's wallet should auto-connect, resulting in a seamless experience.
