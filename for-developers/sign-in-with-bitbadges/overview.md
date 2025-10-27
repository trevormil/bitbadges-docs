# Overview

Sign In with BitBadges (SIWBB) is a unified multi-chain authentication solution that works across all blockchain ecosystems. It replaces traditional "Sign In with X" buttons and can be configured to handle the following all in one flow:

* Multi-chain authentication
* Token ownership verification
* Integration with 7000+ supported apps and plugins
* BitBadges API scope authorizations

#### Use Alternatives If Possible

NOTE: This is mainly intended for OAuth authorization for the BitBadges API. If you don't need this, then we recommend you use other Web3 services out there for Web3 authentication now like WalletConnect, MagicAuth, etc.&#x20;

Combining authentication + criteria checks (claim successes, token ownership) is a powerful combination with the BitBadges API. Simply 1) authenticate and 2) check criteria using our API.

```typescript
// Pre-req: Create claim in BitBadges site
// 1. Authenticate your user (using your existing setup)
// 2. Verify claim success
const res = await BitBadgesApi.checkClaimSuccess(claimId, address);
```

#### OAuth Endpoints

* **Authorization:** `https://bitbadges.io/siwbb/authorize?your_params`
* **Token:** `https://api.bitbadges.io/api/v0/siwbb/token`
* **Revoke:** `https://api.bitbadges.io/api/v0/siwbb/token/revoke`

[**Demo**](https://bitbadges.io/siwbb/authorize?client_id=example-client-id\&redirect_uri=https://example.com&)

## Implementation Flow

1. **Authentication- BitBadges Side**
   * User accesses BitBadges URL (direct or popup)
   * Proves address ownership and other criteria (tokens, etc.)
   * Receives authorization code from BitBadges (QR or behind the scenes via redirect)
2. **Integration- Your App Side**
   * Receive authentication response
   * Check criteria on your end
   * Implements security measures (prevent replay attacks, etc.)
   * Focus on your core utility
