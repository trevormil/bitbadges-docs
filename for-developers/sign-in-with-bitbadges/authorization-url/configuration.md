# Configuration

BitBadges authentication uses an OAuth URL with custom parameters: `https://bitbadges.io/siwbb/authorize?client_id=...`.

Users will visit this URL to authenticate and receive an authorization code. This code will be passed behind the scenes for digital flows via the redirect or it will be the actual QR code for in-person / delayed flows.

By default, Sign In with BitBadges will handle multi-chain authentication for the user (in other words, checking address ownership). You can additionally:

* Specify the `scope` to request additional BitBadges API permissions for the user (e.g. 'completeClaims,readClaimAlerts')
* Specify a `claimId` to display a specific claim to the user (for display purposes, you will need to verify the claim successes on your end). Claims open up any criteria like token ownership checks, payments, anything

## Parameter Interface

```typescript
interface CodeGenQueryParams {
    client_id: string; // Required: Your app's client ID
    redirect_uri?: string; // Required for instant auth. Not needed for QR code auth.
    state?: string; // Optional: Additional data passed to redirect
    scope?: string; // Optional: Comma-separated BitBadges API permission scopes (e.g. 'completeClaims,readClaimAlerts')

    // Claim UI Options (optional)
    claimId?: string; // ID of required claim
    hideIfAlreadyClaimed?: boolean; // Hide if already claimed
    expectVerifySuccess?: boolean;

    // Expect attestation presentations
    expectAttestations?: boolean;
}
```

## Key Features

### 1. App Configuration

* Create OAuth apps at [bitbadges.io/developer](https://bitbadges.io/developer)
* `client_id` is mandatory (obtained from app registration)
* `redirect_uri` required for instant auth, optional for delayed auth
* If `redirect_uri` is blank, QR code is generated and stored in user's account
* See OAuth tutorials for more details

### 2. Scopes

* This is only needed for authorized BitBadges API access. By default, you will verify address ownership with no scopes.
* Format: `scope: 'completeClaims,readClaimAlerts'`
* View all available scopes at [bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen)

### 3. Claims

* Specify required claim to be satisfied with `claimId`
* Create the claim in the developer portal. See claim docs for more details.
* Claims are not a part of the core authentication process. You need to verify them separately server-side to ensure the user has met the criteria.
