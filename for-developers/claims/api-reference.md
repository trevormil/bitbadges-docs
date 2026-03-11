# Claims API Reference

## Completing Claims

```typescript
// Both address formats work (Ethereum 0x or BitBadges bb-prefixed)
const res = await BitBadgesApi.completeClaim(claimId, address, body);
console.log(res.claimAttemptId);

// Wait ~2 seconds for processing, then check status
const status = await BitBadgesApi.getClaimAttemptStatus(res.claimAttemptId);
console.log(status); // { success: true }
```

### Request Body

```typescript
{
  _expectedVersion: '0',  // Required: claim.version (use -1 to unsafely skip check — not recommended)
  [instanceId]: {          // Per-plugin inputs, keyed by instance ID
    ...pluginInputs
  }
}
```

**Example:**

```typescript
await BitBadgesApi.completeClaim(claimId, 'bb1...', {
    _expectedVersion: '0',
    'password-instance': { password: 'secret123' },
    'codes-instance': { code: 'abc-def-ghi' },
});
```

### Notes

- `_expectedVersion` prevents the claim creator from changing criteria without you knowing. Obtain from `getClaim()`.
- If sign-in is required, you must have proper session authentication.
- If sign-in is disabled, gate the claim another way (e.g., password only your backend knows).
- The `completeClaim` route auto-simulates first. If simulation fails, it returns immediately without queuing.

## Simulating Claims

```typescript
const res = await BitBadgesApi.simulateClaim(claimId, address, body);
```

- Instant (not queued) — returns result immediately
- Same body format as `completeClaim`
- Use `_specificInstanceIds` to simulate only certain plugins:

```typescript
await BitBadgesApi.simulateClaim(claimId, address, {
  _expectedVersion: '0',
  _specificInstanceIds: ['plugin-a', 'plugin-b'],  // Only test these
  'plugin-a': { ... }
});
```

## Verifying Claim Attempts

### By Address

```typescript
// GET /api/v0/claims/success/{claimId}/{address}
const res = await BitBadgesApi.checkClaimSuccess(claimId, address);
if (res.successCount >= 1) {
    // User has successfully claimed
}
```

- `successCount` = number of completions (1 for on-demand, N for standard)
- Both `0x` and `bb1` address formats work

### By Attempt ID

```typescript
// GET /api/v0/claims/status/{claimAttemptId}
const res = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);
if (res.success) {
    // This specific attempt succeeded
}
```

Obtain `claimAttemptId` from the `completeClaim()` response or from a custom plugin.

### Polling Pattern

Claims process asynchronously. After calling `completeClaim()`, poll for the result:

```typescript
const res = await BitBadgesApi.completeClaim(claimId, address, body);

// Poll every 2-3 seconds until resolved
const pollStatus = async (
    attemptId: string,
    maxRetries = 10,
): Promise<boolean> => {
    for (let i = 0; i < maxRetries; i++) {
        await new Promise((r) => setTimeout(r, 2000));
        const status = await BitBadgesApi.getClaimAttemptStatus(attemptId);
        if (status.success !== undefined) return status.success;
    }
    throw new Error('Claim processing timed out');
};

const success = await pollStatus(res.claimAttemptId);
```

Typical processing time is 1-5 seconds. Claims for the same collection process sequentially; different collections process in parallel.

## Fetching Claims

```typescript
const claim = await BitBadgesApi.getClaim({
    claimId: 'your-claim-id',
    fetchPrivateParams: false, // true = include private params (owner only + auth)
    fetchAllClaimedUsers: true, // Include list of all claimed users
    privateStatesToFetch: ['instanceId'], // Fetch private state for specific plugins
});
```

## Looking Up Plugins

### Get a Single Plugin

```typescript
// GET /api/v0/plugins/{pluginId}
const plugin = await BitBadgesApi.getPlugin('must-own-badges');

// Inspect the latest version's schemas
const latest = plugin.versions[plugin.versions.length - 1];
console.log(latest.userInputsSchema); // What users provide
console.log(latest.publicParamsSchema); // Creator-configured public params
console.log(latest.privateParamsSchema); // Creator-configured private params
console.log(latest.verificationCall); // HTTP endpoint config
console.log(latest.stateFunctionPreset); // 'Stateless' | 'ClaimToken' | 'ClaimNumbers' | 'CustomResponseHandler'
```

### Fetch Multiple Plugins

```typescript
// POST /api/v0/plugins/fetch
const res = await BitBadgesApi.getPlugins({
    pluginIds: ['must-own-badges', 'min-badge', 'url-clicker'],
    returnSensitiveData: false, // true = include pluginSecret (owner only)
});
```

### Search Published Plugins

```typescript
// GET /api/v0/plugins/search
const res = await BitBadgesApi.searchPlugins({
    searchValue: 'badge', // Filter by name
    bookmark: undefined, // Pagination cursor
    locale: 'en',
});
```

### Get Plugin Errors

```typescript
const errors = await BitBadgesApi.getPluginErrors({
    pluginId: 'your-plugin-id',
    // ... pagination options
});
```

Useful for debugging failed plugin executions during development.

## Deleting Claims

```typescript
await BitBadgesApi.deleteClaims({ claimIds: ['claim-id-1', 'claim-id-2'] });
```

Deletion is a soft-delete — the claim is marked with `deletedAt` and excluded from queries. Existing claim attempt records are preserved for history.

## Searching Claims

```typescript
const res = await BitBadgesApi.searchClaims({
    searchValue: 'nft mint',
    bookmark: undefined, // Pagination cursor
});
```

Only claims with `showInSearchResults: true` appear in search results.
