# Claim Examples

Full end-to-end examples showing how to configure and complete claims.

## Claim Configuration

A claim is defined by an `iClaimBuilderDoc`. Each plugin in the `plugins` array has this shape:

```typescript
interface IntegrationPluginParams<T extends ClaimIntegrationPluginType> {
  instanceId: string;       // Unique ID for this plugin instance within the claim
  pluginId: T;              // Plugin type: "numUses", "codes", "password", etc.
  version: string;          // Plugin version (usually "0")
  publicParams: object;     // Plugin-specific config (visible to users)
  privateParams: object;    // Plugin-specific secrets (hidden from users)
}
```

## Example: Code-Gated Claim

A claim where users redeem one-time codes. Limited to 100 claims total.

```typescript
import crypto from 'crypto';
import CryptoJS from 'crypto-js';

const seedCode = crypto.randomBytes(32).toString('hex');
const numCodes = 100;

// Generate codes from seed
const codes = [];
for (let i = 0; i < numCodes; i++) {
  const hash = CryptoJS.SHA256(`${seedCode}-${i}`).toString();
  codes.push(`${hash}-${i}`);
}

const claim: iClaimBuilderDoc = {
  plugins: [
    {
      pluginId: 'numUses',
      instanceId: 'num-uses-instance',
      version: '0',
      publicParams: { maxUses: 100 },
      privateParams: {}
    },
    {
      pluginId: 'codes',
      instanceId: 'codes-instance',
      version: '0',
      publicParams: { numCodes: 100 },
      privateParams: { codes, seedCode }
    }
  ],
  state: {},
  action: { seedCode },
  // ... other required fields (collectionId, createdBy, etc.)
};
```

**Completing this claim:**

```typescript
const res = await BitBadgesApi.completeClaim(claimId, 'bb1...', {
  _expectedVersion: '0',
  'codes-instance': { code: codes[0] }  // Keyed by instanceId
});

// Check status after ~2 seconds
const status = await BitBadgesApi.getClaimAttemptStatus(res.claimAttemptId);
```

## Example: Password-Gated Auto-Completion

A backend-driven claim where your server completes claims on behalf of users. No sign-in required — gated by a password only your backend knows.

```typescript
const claim: iClaimBuilderDoc = {
  plugins: [
    {
      pluginId: 'numUses',
      instanceId: 'num-uses',
      version: '0',
      publicParams: { maxUses: 1000, displayAsUnlimited: true },
      privateParams: {}
    },
    {
      pluginId: 'password',
      instanceId: 'backend-password',
      version: '0',
      publicParams: {},
      privateParams: { password: 'my-backend-secret-password' }
    }
  ],
  state: {},
  action: {},
  approach: 'api',
  // ... other required fields
};
```

**Auto-completing from your backend:**

```typescript
// Your backend completes the claim on behalf of the user
await BitBadgesApi.completeClaim(claimId, userAddress, {
  _expectedVersion: '0',
  'backend-password': { password: 'my-backend-secret-password' }
});
```

## Example: Whitelist + Token Ownership (AND Logic)

User must be on a whitelist AND own a specific badge. Both plugins must pass (default AND logic).

```typescript
const claim: iClaimBuilderDoc = {
  plugins: [
    {
      pluginId: 'numUses',
      instanceId: 'num-uses',
      version: '0',
      publicParams: { maxUses: 500 },
      privateParams: {}
    },
    {
      pluginId: 'initiatedBy',
      instanceId: 'sign-in',
      version: '0',
      publicParams: {},
      privateParams: {}
    },
    {
      pluginId: 'whitelist',
      instanceId: 'vip-list',
      version: '0',
      publicParams: { maxUsesPerAddress: 1 },
      privateParams: {
        list: {
          listId: '',
          addresses: ['bb1abc...', 'bb1def...', 'bb1ghi...'],
          whitelist: true
        }
      }
    },
    {
      pluginId: 'must-own-badges',
      instanceId: 'badge-gate',
      version: '0',
      publicParams: {},
      privateParams: {
        ownershipRequirements: {
          $and: [{
            assets: [{
              collectionId: '1',
              assetIds: [{ start: '1', end: '1' }],
              ownershipTimes: [{ start: '1', end: 'MAX' }],
              mustOwnAmounts: { start: '1', end: 'MAX' }
            }]
          }]
        }
      }
    }
  ],
  state: {},
  action: {},
  // ... other required fields
};
```

## Example: OR Logic with Multiple Paths

User can claim by entering a code OR by being on the whitelist. Uses `satisfyMethod` for OR logic. Note `numUses` is always required regardless of success logic.

```typescript
const claim: iClaimBuilderDoc = {
  plugins: [
    {
      pluginId: 'numUses',
      instanceId: 'num-uses',
      version: '0',
      publicParams: { maxUses: 200 },
      privateParams: {}
    },
    {
      pluginId: 'codes',
      instanceId: 'codes-path',
      version: '0',
      publicParams: { numCodes: 100 },
      privateParams: { codes: [...], seedCode: '...' }
    },
    {
      pluginId: 'whitelist',
      instanceId: 'whitelist-path',
      version: '0',
      publicParams: { maxUsesPerAddress: 1 },
      privateParams: {
        useDynamicStore: true,
        dynamicDataId: 'my-store-id',
        dataSecret: 'my-store-secret'
      }
    }
  ],
  satisfyMethod: {
    type: 'OR',
    conditions: ['codes-path', 'whitelist-path']
    // numUses is always required and not included in OR conditions
  },
  state: {},
  action: {},
  // ... other required fields
};
```

**Completing via the code path:**

```typescript
await BitBadgesApi.completeClaim(claimId, 'bb1...', {
  _expectedVersion: '0',
  _specificInstanceIds: ['codes-path'],  // Only attempt the code path
  'codes-path': { code: 'abc-123' }
});
```

## Example: Time-Windowed Claim

Only claimable during a specific time window.

```typescript
const claim: iClaimBuilderDoc = {
  plugins: [
    {
      pluginId: 'numUses',
      instanceId: 'num-uses',
      version: '0',
      publicParams: { maxUses: 50 },
      privateParams: {}
    },
    {
      pluginId: 'transferTimes',
      instanceId: 'time-window',
      version: '0',
      publicParams: {
        transferTimes: [{
          start: '1741564800000',  // March 10, 2025 00:00 UTC
          end: '1742169600000'     // March 17, 2025 00:00 UTC
        }]
      },
      privateParams: {}
    },
    {
      pluginId: 'initiatedBy',
      instanceId: 'sign-in',
      version: '0',
      publicParams: {},
      privateParams: {}
    }
  ],
  state: {},
  action: {},
  // ... other required fields
};
```

## Simulating Before Completing

Always simulate first to check if a claim would succeed:

```typescript
// Simulate (instant, no side effects)
const sim = await BitBadgesApi.simulateClaim(claimId, 'bb1...', {
  _expectedVersion: '0',
  'codes-instance': { code: 'abc-123' }
});

// If simulation passes, complete for real
const res = await BitBadgesApi.completeClaim(claimId, 'bb1...', {
  _expectedVersion: '0',
  'codes-instance': { code: 'abc-123' }
});
```

## Verifying Claim Success

```typescript
// Check if an address has claimed
const result = await BitBadgesApi.checkClaimSuccess(claimId, 'bb1...');
if (result.successCount >= 1) {
  // Grant access, show content, etc.
}

// Check a specific attempt
const status = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);
if (status.success) {
  // This attempt succeeded
}
```
