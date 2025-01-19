# Configuration

BitBadges authentication uses an OAuth URL with custom parameters: `https://bitbadges.io/siwbb/authorize?client_id=...`. Users will visit this URL to authenticate and receive an authorization code. This code will be passed behind the scenes for digital flows via the redirect or it will be the actual QR code for in-person / delayed flows.

By default, Sign In with BitBadges will handle multi-chain authentication for the user (in other words, checking address ownership). You can additionally:

* Specify the `scope` to request additional BitBadges API permissions for the user
* Specify a `claimId` to display a specific claim to the user

**"Attaching" a Claim**

And what does attaching a BitBadges claim open up?

* Check other social sign-ins (e.g. Discord, Twitter, GitHub, Google)
* Check badge ownership, receive private attestations from the user accounts, check criteria from the 7000+ connected apps and integrations, and much more!

Treat claims as just "attached" and not a part of the core process. You need to verify it separately server-side accordingly to your needs. Specifying the `claimId` in the URL parameters will display it to the user, but it does not guarantee verification. You will use the combination of 1) Sign In with BitBadges address authentication and 2) looking up the successful claim attempt for that address to verify the current user has satisfied the claim criteria.

Along similar lines, you may not even want to display the claim criteria to the user. If this is the case, you can simply omit the `claimId` parameter and just server-side verify the claim criteria.

## Parameter Interface

```typescript
interface CodeGenQueryParams {
    client_id: string; // Required: Your app's client ID
    redirect_uri?: string; // Required for instant auth. Not needed for QR code auth.
    state?: string; // Optional: Additional data passed to redirect
    scope?: string; // Optional: Comma-separated BitBadges API permission scopes (e.g. 'Complete Claims,Read Address Lists')

    // Claims (optional)
    claimId?: string; // ID of required claim
    hideIfAlreadyClaimed?: boolean; // Hide if already claimed
    expectVerifySuccess?: boolean;
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
* Format: `scope: 'Complete Claims,Read Address Lists'`
* View available scopes at [bitbadges.io/auth/linkgen](https://bitbadges.io/auth/linkgen)

### 3. Claims

* Specify required claim to be satisfied with `claimId`
* Create the claim in the developer portal. See claim docs for more details.
* `hideIfAlreadyClaimed` hides if user passed claim
* `expectVerifySuccess` is used by us to catch errors early. If the user fails to meet claim criteria, we will not allow them to proceed.
* Claims are not a part of the core authentication process. You need to verify them separately server-side to ensure the user has met the criteria.
