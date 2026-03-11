# Built-In Plugins

This page documents the schemas for all core plugins and pre-built plugin-based plugins.

## Core Plugins

Core plugins are built into BitBadges and handled in-memory. They require no external HTTP calls.

### `codes` — Claim Codes

One-time use codes that users must present to claim.

**User Input:**
```typescript
{ code: string }
```

**Public Params:**
```typescript
{
  numCodes: number;           // Total number of codes
  hideCurrentState?: boolean; // Hide usage state from users
}
```

**Private Params:**
```typescript
{
  codes: string[];   // The actual codes
  seedCode: string;  // Seed used to generate codes
}
```

**Public State:**
```typescript
{
  usedCodeRanges?: UintRange[];  // Ranges of used code indices
}
```

---

### `password` — Password Gate

Gate claims behind a shared secret.

**User Input:**
```typescript
{ password: string }
```

**Private Params:**
```typescript
{
  password: string;  // The password to check against
}
```

No public params or state.

---

### `numUses` — Max Claims Limit

Limit the total number of successful claims. This plugin is **always required** and cannot be made optional via success logic.

**Public Params:**
```typescript
{
  maxUses: number;              // Maximum total claims allowed
  hideCurrentState?: boolean;   // Hide current count from users
  displayAsUnlimited?: boolean; // Display as "unlimited" in UI
}
```

**Public State:**
```typescript
{
  numUses?: number;                          // Current claim count
  usedClaimNumbers?: UintRange[];            // Which claim numbers are taken
  claimedUsers?: {
    [bitbadgesAddress: string]: number[];    // Claim numbers per address
  };
}
```

No user input.

---

### `transferTimes` — Time Windows

Restrict when claims can be completed.

**Public Params:**
```typescript
{
  transferTimes: UintRange[];  // Array of allowed time ranges (UNIX ms)
}
```

No user input, state, or private params.

---

### `initiatedBy` — Sign-In Requirement

Requires the user to be signed in to BitBadges. This verifies the claiming address is authenticated.

No configurable params — configuration is handled in the claim builder UI.

---

### `whitelist` — Address List Gate

Gate claims to a specific set of addresses. Supports both static address lists and dynamic stores.

**Public Params:**
```typescript
{
  listId?: string;              // Reference to an existing address list
  list?: AddressList;           // Inline address list
  maxUsesPerAddress?: number;   // Max claims per address
  hasPrivateList?: boolean;     // True if the list is hidden from public
}
```

**Private Params:**
```typescript
{
  useDynamicStore?: boolean;  // Use a dynamic store instead of static list
  dynamicDataId?: string;     // Dynamic store ID
  dataSecret?: string;        // Dynamic store secret
  listId?: string;            // Address list ID (private variant)
  list?: AddressList;         // Inline list (private variant)
}
```

**Private State:**
```typescript
{
  addresses: { [address: string]: number };  // Claims per address
}
```

---

### `halt` — Pause Claims

Temporarily halt all claim attempts. No params — just add/remove from the plugin list.

---

### `anonymous` — Anonymous Claiming

Allow claims without requiring an address. No params.

---

## Pre-Built Custom Plugins

These are pre-built plugins available by their plugin ID. They function as custom plugins (HTTP-based) but are maintained by BitBadges.

To look up their full schema (user inputs, public/private params), use the API:

```typescript
const plugin = await BitBadgesApi.getPlugin('must-own-badges');
// plugin.versions[0].userInputsSchema
// plugin.versions[0].publicParamsSchema
// plugin.versions[0].privateParamsSchema
```

| Plugin ID | Description |
|-----------|-------------|
| `must-own-badges` | Require ownership of specific badges/tokens. Configure which collection + token IDs. |
| `min-badge` | Require a minimum token/badge balance. |
| `url-clicker` | Require the user to visit a specific URL. |
| `custom-instructions` | Display custom instructions to the user (informational only). |
| `satisfies-claim` | Require the user to have completed another claim first. |
| `username-set` | Require the user to have set a BitBadges username. |

## Looking Up Plugin Schemas

You can look up any plugin's configuration schema via the API:

```typescript
// Get a single plugin by ID
const res = await BitBadgesApi.getPlugin('must-own-badges');
const latestVersion = res.versions[res.versions.length - 1];

console.log(latestVersion.userInputsSchema);    // What users must provide
console.log(latestVersion.publicParamsSchema);   // Creator-configured public params
console.log(latestVersion.privateParamsSchema);  // Creator-configured private params
console.log(latestVersion.stateFunctionPreset);  // State management type
console.log(latestVersion.verificationCall);     // HTTP endpoint config

// Fetch multiple plugins at once
const res = await BitBadgesApi.getPlugins({
  pluginIds: ['must-own-badges', 'min-badge', 'url-clicker'],
  returnSensitiveData: false  // true = include pluginSecret (owner only)
});

// Search published plugins by name
const res = await BitBadgesApi.searchPlugins({
  searchValue: 'badge',
  bookmark: undefined,  // For pagination
  locale: 'en'
});
```

### Plugin Version Config

Each plugin version contains the full configuration:

```typescript
interface PluginVersionConfig {
  version: number;
  finalized: boolean;              // Only finalized versions are usable
  stateFunctionPreset: 'Stateless' | 'ClaimToken' | 'ClaimNumbers' | 'CustomResponseHandler';
  duplicatesAllowed: boolean;      // Can a claim have multiple instances?
  requiresSessions: boolean;       // Needs authenticated session?
  requiresUserInputs: boolean;     // Needs user interaction?
  reuseForNonIndexed: boolean;     // Compatible with on-demand claims?
  receiveStatusWebhook: boolean;   // Receive status updates?
  skipProcessingWebhook?: boolean; // Auto-pass without calling endpoint?
  ignoreSimulations?: boolean;     // Skip during dry-runs?
  requireSignIn?: boolean;         // Require BitBadges sign-in?

  // Schemas (JsonBodyInputSchema arrays)
  userInputsSchema: JsonBodyInputSchema[];
  publicParamsSchema: JsonBodyInputSchema[];
  privateParamsSchema: JsonBodyInputSchema[];

  // HTTP endpoint configuration
  verificationCall?: {
    uri: string;
    hardcodedInputs: JsonBodyInputWithValue[];
    passAddress?: boolean;
  };

  // UI customization
  customDetailsDisplay?: string;   // Template: "Requires {{minBalance}} tokens"
}
```

### Schema Field Types

Each schema field (`JsonBodyInputSchema`) describes one input:

```typescript
interface JsonBodyInputSchema {
  key: string;                 // Field name in payload
  label: string;               // Display label
  type: string;                // 'string' | 'number' | 'boolean' | 'date' | 'url'
  required?: boolean;
  defaultValue?: string | number | boolean;
  helper?: string;             // Help text
  options?: { label: string; value: string | number | boolean }[];  // Dropdown
  arrayField?: boolean;        // Is this an array?
  headerField?: boolean;       // Pass as HTTP header instead of body?
  hideFromDetailsDisplay?: boolean;  // Hide from public view (public params only)
  hyperlink?: { url: string; showAsGenericView?: boolean };
}
```
