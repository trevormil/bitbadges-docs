# Sign In with BitBadges Overview

Sign In with BitBadges (SIWBB) is a unified multi-chain authentication solution that works across all blockchain ecosystems. It replaces traditional "Sign In with X" buttons and handles:

-   Multi-chain authentication
-   Verification of attestation signatures
-   Badge and NFT ownership verification
-   Integration with supported apps and plugins
-   BitBadges API scope authorizations

Outsource the heavy lifting of authentication to us, allowing you to focus on your core utility. We aim to provide maximum flexibility in the design process.

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## OAuth Endpoints

-   **Authorization:** `https://bitbadges.io/siwbb/authorize`
-   **Token:** `https://api.bitbadges.io/api/v0/siwbb/token`
-   **Revoke:** `https://api.bitbadges.io/api/v0/siwbb/token/revoke`

## What SIWBB Handles

SIWBB manages the core authentication flow and can additionally verify:

-   Proof of address ownership
-   Asset ownership (badges, NFTs, etc.)
-   Attestation signatures
-   Social account connections (Discord, GitHub, etc.)

## Authentication Methods

### 1. Digital Authentication (Immediate)

Best for web applications where users can authenticate immediately:

-   User clicks "Sign In with BitBadges"
-   Popup window opens for authentication
-   Verification happens instantly
-   Perfect for badge-gated websites

### 2. QR Code Authentication (Delayed)

Ideal for in-person or offline scenarios:

-   Pre-generate authentication QR codes
-   Users can authenticate ahead of time
-   Verify credentials at point of use
-   Great for ticket verification or physical access control

## Implementation Flow

1. **Authentication- BitBadges Side**

    - User accesses BitBadges URL (direct or popup)
    - Proves address ownership and other criteria (badges, attestations, etc.)
    - Receives authorization code from BitBadges (QR or behind the scenes)

2. **Integration- Your App Side**
    - Receives authentication response
    - Manages user sessions
    - Implements security measures (prevent replay attacks, etc.)

## Getting Started

Check out our [BitBadges quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) for a complete implementation example and reference code.

Note: SIWBB is fully OAuth 2.0 compatible and works with standard OAuth frameworks and tools.
