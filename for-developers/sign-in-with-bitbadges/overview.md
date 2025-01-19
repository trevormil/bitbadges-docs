# Overview

Sign In with BitBadges (SIWBB) is a unified multi-chain authentication solution that works across all blockchain ecosystems. It replaces traditional "Sign In with X" buttons and can be configured to handle the following all in one flow:

* Multi-chain authentication
* Verification of attestation signatures
* Badge ownership verification
* Integration with 7000+ supported apps and plugins
* BitBadges API scope authorizations

Outsource the heavy lifting of authentication to us, allowing you to focus on your core utility. We aim to provide maximum flexibility in the design process.

[**Demo**](https://bitbadges.io/siwbb/authorize?expectAttestationsPresentations=true\&client_id=example-client-id\&redirect_uri=https://example.com&)

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

## OAuth Endpoints

* **Authorization:** `https://bitbadges.io/siwbb/authorize`
* **Token:** `https://api.bitbadges.io/api/v0/siwbb/token`
* **Revoke:** `https://api.bitbadges.io/api/v0/siwbb/token/revoke`

## What SIWBB Handles

SIWBB manages the core authentication flow:

* Proof of address ownership

And by attaching a BitBadges calim, you can seamlessly verify:

* Asset ownership (badges, NFTs, etc.)
* Attestations
* Social account connections (Discord, GitHub, etc.)
* Criteria checks from your favorite app!

## Authentication Methods

### 1. Digital Authentication (Immediate)

Best for web applications where users can authenticate immediately:

* User clicks "Sign In with BitBadges"
* Popup window opens for authentication
* Verification happens instantly
* Perfect for badge-gated websites

### 2. QR Code Authentication (Delayed)

Ideal for in-person or offline scenarios:

* Pre-generate authentication QR codes
* Users can authenticate ahead of time. No need for wallets at presentation time.
* Verify credentials at point of use
* Great for ticket verification or physical access control

## Implementation Flow

1. **Authentication- BitBadges Side**
   * User accesses BitBadges URL (direct or popup)
   * Proves address ownership and other criteria (badges, attestations, etc.)
   * Receives authorization code from BitBadges (QR or behind the scenes)
2. **Integration- Your App Side**
   * Receive authentication response
   * Implements security measures (prevent replay attacks, etc.)
   * Focus on your core utility

## Getting Started

Check out our [BitBadges quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) for a complete implementation example and reference code.

Note: SIWBB is fully OAuth 2.0 compatible and works with standard OAuth frameworks and tools.
