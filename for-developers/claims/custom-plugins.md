# Custom Plugins

Custom plugins are HTTP endpoints that extend claim logic. BitBadges sends a POST request to your endpoint during simulation, execution and/or post-success, and your response determines whether the plugin passes or fails.

Any logic you can express as an HTTP handler can become a claim plugin — authentication checks, API calls, database lookups, AI evaluations, webhook triggers, or anything else.

## Getting Started

1. Go to [bitbadges.io/developer](https://bitbadges.io/developer) → Plugins tab
2. Create a new plugin
3. Configure endpoint URL, schemas, and settings
4. Implement your handler
5. Test with the built-in claim tester
6. Finalize the version
7. Use the plugin in claims by its plugin ID

## Architecture

```
User claims → BitBadges sends POST to your endpoint → You return 200 OK or error
```

**Three parties:**
- **Claim Creator** — configures the claim, selects plugins, sets public/private params
- **Claiming User** — attempts the claim, provides user inputs
- **Plugin Creator** — builds and maintains the plugin endpoint (you)

The claim creator configures public/private params when adding the plugin to a claim. The user provides user inputs at claim time. All three are merged into a single request payload sent to your handler.

## Handler Implementation

```typescript
import { NextApiRequest, NextApiResponse } from 'next';

const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  try {
    const {
      // Context
      pluginSecret,         // Verify BitBadges as the caller
      claimId,              // Which claim is being attempted
      claimAttemptId,       // Unique attempt ID (empty for simulations)
      _isSimulation,        // true = dry run, don't execute side effects
      _attemptStatus,       // 'executing' during claim processing
      lastUpdated,          // Claim last updated timestamp (UNIX ms)
      createdAt,            // Claim creation timestamp (UNIX ms)
      version,              // Claim version string
      locale,               // User's locale

      // User identification
      bitbadgesAddress,     // bb-prefixed Cosmos address
      ethAddress,           // 0x-prefixed Ethereum address
      isAddressSignedIn,    // true if address is verified via sign-in

      // Claim number context
      assignMethod,         // Claim number assignment method
      isClaimNumberAssigner, // true if this plugin assigns claim numbers

      // Your custom inputs (merged from all param sources)
      ...customInputs
    } = req.body;

    // 1. Verify origin
    if (pluginSecret !== process.env.PLUGIN_SECRET) {
      return res.status(401).json({ message: 'Invalid plugin secret' });
    }

    // 2. Handle simulations
    if (_isSimulation) {
      // Validate inputs, check preconditions, but don't mutate state
      return res.status(200).json({});
    }

    // 3. Your custom logic
    // ...

    // 4. Return response based on your preset
    return res.status(200).json({});
  } catch (err) {
    return res.status(401).json({ message: String(err) });
  }
};

export default handlePlugin;
```

## Context Info Reference

Every request includes these context fields:

```typescript
interface ContextInfo {
  locale: string;               // User's locale
  bitbadgesAddress: string;     // bb-prefixed address (if passAddress enabled)
  ethAddress: string;           // 0x address (if applicable)
  isAddressSignedIn: boolean;   // Whether the address is verified
  claimId: string;              // The claim being attempted
  _isQueueHandler: boolean;     // Internal: true if processing from queue
  _isSimulation: boolean;       // true for dry-run attempts
  _attemptStatus: string;       // 'executing' during processing
  lastUpdated: number;          // Claim last updated (UNIX ms)
  createdAt: number;            // Claim creation time (UNIX ms)
  claimAttemptId: string;       // Unique attempt ID (empty for simulations)
  assignMethod: string;         // Claim number assignment method
  isClaimNumberAssigner: boolean; // true if this plugin assigns claim numbers
}
```

Additionally, `pluginSecret` and `version` are included in every request. All custom params (public, private, user inputs) are merged into the top level of the request body alongside these fields.

## Parameters

Three types of parameters, all merged into the handler payload:

```typescript
{
  ...contextFields,     // pluginSecret, claimId, addresses, etc.
  ...publicParams,      // Visible to everyone (set by claim creator)
  ...privateParams,     // Visible only to BitBadges + claim creator
  ...userInputs         // Provided by the claiming user at claim time
}
```

- **Public params** — Configured by the claim creator when adding the plugin to a claim. Visible in the claim's public configuration. Claim users can see these. Example: minimum token balance, a URL to display.
- **Private params** — Configured by the claim creator. Hidden from claim users, only visible to BitBadges and the claim creator. Example: API keys, internal thresholds, webhook URLs.
- **User inputs** — Provided by the user at claim time via a form rendered by BitBadges (or a custom frontend). Example: a code, an answer to a question, a file upload URL.

All three are defined as `JsonBodyInputSchema[]` arrays in your plugin's version config.

## User Inputs

### In-Site Forms (Recommended)

Define input schemas when creating your plugin. BitBadges renders a form for the user based on your schema.

Each field is defined as:

```typescript
interface JsonBodyInputSchema {
  key: string;                 // Field name in payload
  label: string;               // Display label shown to user
  type: string;                // 'string' | 'number' | 'boolean' | 'date' | 'url'
  required?: boolean;          // Is this field required?
  defaultValue?: string | number | boolean;  // Pre-filled value
  helper?: string;             // Help text displayed below the field
  options?: {                  // If provided, renders a dropdown select
    label: string;
    value: string | number | boolean;
  }[];
  arrayField?: boolean;        // Render as array input (multiple values)
  headerField?: boolean;       // Pass as HTTP header instead of body
  hideFromDetailsDisplay?: boolean;  // Hide from public display (for public params)
  hyperlink?: {                // Link associated with this field
    url: string;
    showAsGenericView?: boolean;
  };
}
```

**Supported types:**
- `string` — free text input
- `number` — numeric input
- `boolean` — checkbox/toggle
- `date` — date picker (value is UNIX ms)
- `url` — URL input with validation

### Custom Frontend

If the in-site form isn't sufficient, redirect users to your own UI via a redirect URL. Typical flow:

1. User is redirected to your frontend
2. User authenticates / completes action on your side
3. You issue a one-time code or token
4. User enters the code back in-site
5. Your handler validates the code

## Response Presets

Configure your plugin's `stateFunctionPreset` when creating a version. This determines how BitBadges handles your response and manages state.

### `Stateless`

Return `200 OK` with an empty body. Plugin passes if status is 200. No state is tracked.

```json
{}
```

Best for: validation checks, API verifications, webhook triggers — anything where you just need pass/fail.

### `ClaimToken`

Return a one-time use token that you generate. BitBadges tracks which tokens have been used. If the overall claim succeeds, the token is marked as used. If the claim fails (another plugin fails), the token remains available for retry.

```json
{ "claimToken": "unique-token-abc123" }
```

Best for: custom authentication flows, external code validation, third-party integrations that issue tokens.

### `ClaimNumbers`

Return the claim number to assign for this attempt. Only **one plugin** per claim can use this preset.

```json
{ "claimNumber": 0 }
```

Best for: custom claim number assignment logic (non-sequential, based on external data, etc.).

### `CustomResponseHandler`

Full control over state management and response handling. You manage everything yourself.

Best for: advanced use cases that don't fit the other presets.

## Error Handling

- **Timeout:** All responses must arrive within **10 seconds**. If your endpoint doesn't respond in time, the plugin fails.
- **Error format:** Return `{ "message": "..." }` with a non-200 status code. The message may be shown to the user — be informative but don't reveal secrets or internal details.
- **Key restriction:** Do not use `.` in returned JSON keys (e.g., use `bob@abc[dot]com` not `bob@abc.com`). This is a storage constraint.

## Simulations (Dry Runs)

BitBadges simulates claims before real submission. Your plugin receives a simulation request first, then (if simulation passes) the real execution request.

**Detection:** `_isSimulation === true` and `claimAttemptId` is empty.

**Your plugin should:**
- Validate inputs and check preconditions
- Return the appropriate status code (200 = would pass, non-200 = would fail)
- **NOT** execute side effects, mutate state, send notifications, or consume tokens

You can opt out of simulation requests by setting `ignoreSimulations: true` in your version config. If opted out, your plugin won't be called during simulation — it will only be called during real execution.

## State Management

**Critical rule:** A 200 from your plugin does NOT mean the overall claim succeeded. Other plugins in the pipeline may fail, causing the claim to fail even though your plugin passed.

### BitBadges-Managed State (Recommended)

Use response presets (`Stateless`, `ClaimToken`, `ClaimNumbers`). BitBadges only commits state changes if:
1. Your plugin returned 200, **AND**
2. The overall claim succeeded (all required plugins passed per the success logic)

This eliminates race conditions and ensures consistency. If the claim fails, any state your plugin would have changed is rolled back.

### Self-Managed State

If you manage state in your own backend (external database, third-party API, etc.):

- **Don't update state on plugin success.** Your plugin passing doesn't mean the claim succeeded.
- **Verify claim outcomes** via the [API](api-reference.md) before committing external state changes:

```typescript
// After some delay, check if the claim actually succeeded
const status = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);
if (status.success) {
  // Now safe to update your external state
}
```

- **Handle race conditions.** Multiple claim attempts can execute concurrently. Use idempotency keys or atomic operations in your backend.
- **Consider using `receiveStatusWebhook`** — BitBadges can POST to your endpoint with the final claim outcome, so you don't have to poll.

### Success Webhooks & Retry Strategy

If your plugin version has `receiveStatusWebhook: true`, BitBadges POSTs to your endpoint after the claim completes with `_attemptStatus: 'success'` or `'failure'` in the body. This is separate from the plugin validation call.

Webhooks use exponential backoff for retries:
- **Base delay:** 1 hour
- **Formula:** `2^retries × base_delay`
- **Max delay:** 7 days
- **Example:** 1h → 2h → 4h → 8h → 16h → ...

Design your webhook handler to be **idempotent** — the same webhook may be delivered multiple times due to retries. Use the `claimAttemptId` as a deduplication key.

## Plugin Version Config

Each plugin version contains the full configuration. When creating or updating a version in the developer portal, you configure all of these fields:

```typescript
interface PluginVersionConfig {
  // Identity
  version: number;                // Auto-incrementing version number
  finalized: boolean;             // Only finalized versions are usable by other users

  // State management
  stateFunctionPreset: 'Stateless' | 'ClaimToken' | 'ClaimNumbers' | 'CustomResponseHandler';

  // Behavior flags
  duplicatesAllowed: boolean;     // Can a claim have multiple instances of this plugin?
  requiresSessions: boolean;      // Does the plugin need an authenticated BitBadges session?
  requiresUserInputs: boolean;    // Does the user need to provide inputs at claim time?
  reuseForNonIndexed: boolean;    // Compatible with on-demand (stateless) claims?
  receiveStatusWebhook: boolean;  // Receive POST with final claim outcome (success/failure)?
  skipProcessingWebhook?: boolean; // Auto-pass without calling your endpoint?
  ignoreSimulations?: boolean;    // Don't call your endpoint during dry-runs?
  requireSignIn?: boolean;        // Require the user to be signed in to BitBadges?

  // Schemas — define what params/inputs your plugin accepts
  userInputsSchema: JsonBodyInputSchema[];    // What the claiming user provides
  publicParamsSchema: JsonBodyInputSchema[];   // What the claim creator configures (public)
  privateParamsSchema: JsonBodyInputSchema[];  // What the claim creator configures (hidden)

  // HTTP endpoint configuration
  verificationCall?: {
    uri: string;                              // Your handler URL
    hardcodedInputs: JsonBodyInputWithValue[]; // Static values always included in requests
    passAddress?: boolean;                    // Include the user's address in the payload
  };

  // UI customization
  customDetailsDisplay?: string;   // Template string shown to users
                                   // Example: "Requires {{minBalance}} tokens"
                                   // Variables reference publicParams keys
}
```

### Field Details

| Field | Description |
|-------|-------------|
| `finalized` | Once finalized, a version is immutable and available to all users. Unfinalized versions are only usable by the plugin creator (for testing). |
| `duplicatesAllowed` | If `true`, a claim can include multiple instances of this plugin (each with different params). If `false`, only one instance is allowed. |
| `requiresSessions` | If `true`, the claim requires an active BitBadges session (user must be signed in). |
| `requiresUserInputs` | If `true`, the user must provide inputs at claim time (BitBadges renders a form). If `false`, the plugin operates with only creator-configured params. |
| `reuseForNonIndexed` | If `true`, the plugin is compatible with on-demand claims (stateless evaluation, no claim action needed). Requires the plugin to be stateless with no user inputs. |
| `receiveStatusWebhook` | If `true`, BitBadges POSTs to your endpoint after the claim completes with the final outcome. Useful for self-managed state — you learn whether the claim succeeded or failed without polling. |
| `skipProcessingWebhook` | If `true`, BitBadges auto-passes this plugin without calling your endpoint. Useful for display-only plugins (like `custom-instructions`) that don't need server-side validation. |
| `ignoreSimulations` | If `true`, your endpoint is not called during simulation/dry-run requests. The plugin auto-passes simulation. |
| `requireSignIn` | If `true`, the user must be signed in to BitBadges before this plugin is evaluated. |
| `hardcodedInputs` | Static key-value pairs always included in requests to your endpoint. Useful for API keys or configuration that doesn't change per claim. |
| `passAddress` | If `true`, the user's `bitbadgesAddress` and `ethAddress` are included in the payload. If `false`, the plugin operates without knowing the user's address. |
| `customDetailsDisplay` | A template string displayed to users in the claim UI. Use `{{key}}` to reference public param values. Example: `"Must own at least {{minBalance}} tokens from collection {{collectionId}}"`. |

## Versioning

Plugins support multiple versions for safe iteration:

- **Creating versions:** Each new version gets an incrementing version number. Start unfinalized for testing.
- **Finalizing:** Once finalized, a version is immutable — schemas, endpoint URL, and all settings are locked. This protects claims that depend on your plugin from breaking changes.
- **Version selection:** When a claim creator adds your plugin, they use the latest finalized version. That version is locked to the claim — even if you release new versions, existing claims stay on the version they were created with.
- **Testing unfinalized versions:** Only the plugin creator can use unfinalized versions. Use the Claim Tester in the developer portal to test before finalizing.
- **Handler versioning:** Your handler receives `version` and `createdAt` in every request. Use these to handle version-specific logic if needed (e.g., backwards-compatible changes to your handler that serves multiple versions).

### Version Lifecycle

```
Create v0 (unfinalized) → Test → Finalize v0 → Claims use v0
Create v1 (unfinalized) → Test → Finalize v1 → New claims use v1, existing claims stay on v0
```

## Publishing to the Plugin Directory

Plugins can be published to the BitBadges plugin directory, making them discoverable by other users.

- **Private plugins** (default) — only you can add them to claims. Useful for internal tools, custom integrations, or plugins that require your specific backend.
- **Published plugins** — visible in the plugin directory. Anyone can add them to their claims. Useful for building reusable tools that serve the broader BitBadges ecosystem.

To publish, configure your plugin's visibility settings in the developer portal. Published plugins should have clear descriptions, well-documented schemas, and stable, finalized versions.

Users can discover published plugins via the API:

```typescript
const results = await BitBadgesApi.searchPlugins({
  searchValue: 'badge ownership',
  bookmark: undefined,
  locale: 'en'
});
```

## Design Considerations

### Plugin Success ≠ Claim Success

Your plugin may pass but the claim fails (other plugins fail). Or your plugin fails but the claim succeeds (OR logic). Only update external state based on verified claim outcomes.

### Parallel Execution

All plugins in a claim execute **in parallel**. Your plugin cannot depend on another plugin's state mutations within the same claim attempt. Each plugin sees state as it was before the attempt started.

### Async Processing

Claims are processed asynchronously via a queue. Don't depend on real-time BitBadges claim state (like total claims completed) — it may be stale by the time your plugin runs. Your own parameters and the context info passed to your handler are safe to depend on.

### Nested Claim Depth

If your plugin uses the `satisfies-claim` pattern (checking another claim's success), be aware that nested on-demand claims have a **depth limit of 5 levels**. Circular references are detected and rejected.

### On-Demand Compatibility

For on-demand claims (stateless, no explicit claim action), your plugin must be:

- **Stateless** — no per-attempt state tracking
- **No user inputs** — operates with just address + hardcoded params
- **Context-only** — works with only the passed context info

Set `reuseForNonIndexed: true` in your version config.

### Authentication

BitBadges does not handle your authentication. If you need the user to authenticate with your service, manage auth end-to-end yourself. Common pattern:

1. User visits your frontend and authenticates
2. You issue a one-time code
3. User enters the code as a user input in-site
4. Your handler validates the code

### Security

- **Verify `pluginSecret`** on every request to confirm BitBadges is the caller
- **Never expose `pluginSecret` client-side** — it should only exist on your backend
- **Sanitize all inputs** — user inputs are untrusted data from the claiming user
- **Rate-limit your endpoint** independently of BitBadges — your endpoint is publicly addressable
- **Don't reveal internal details** in error messages — they may be shown to the user

## Testing

### curl

```bash
curl -X POST https://your-handler.com \
  -H 'Content-Type: application/json' \
  -d '{
    "pluginSecret": "your-secret",
    "claimId": "test-claim",
    "claimAttemptId": "test-attempt",
    "_isSimulation": false,
    "_attemptStatus": "executing",
    "bitbadgesAddress": "bb1...",
    "lastUpdated": 1800000000000,
    "createdAt": 1800000000000,
    "version": "0"
  }'
```

### Developer Portal

- **"Send Test Request"** button sends mocked data to your endpoint — verify your handler responds correctly
- **Claim Tester** — test the full claim flow with your plugin included. This is the only place to test unfinalized versions in a real claim context.

### Request Bin

Use [webhook.site](https://webhook.site) or similar to inspect incoming payloads during development. Set your plugin's endpoint to the request bin URL, trigger a test, and examine exactly what BitBadges sends.

## Internal Plugin Architecture

For reference, core (built-in) plugins follow the same pattern internally:

```typescript
interface BackendIntegrationPlugin<P extends ClaimIntegrationPluginType> {
  pluginId: P;
  metadata: {
    name: string;
    description: string;
    image: string;
    createdBy: string;
    stateless: boolean;         // No state tracking needed
    scoped: boolean;            // State scoped to claim instance
    duplicatesAllowed: boolean; // Multiple instances per claim
    mandatoryToSucceed?: boolean; // Cannot be made optional via success logic
    toSkipIfSuccessfulSimulation: boolean;
  };
  validate: (args: {
    context: ContextInfo & { instanceId: string; pluginId: string; version: string };
    publicParams: PublicParamsType;
    privateParams: PrivateParamsType;
    customBody?: UserInputType;
    priorState?: any;
    adminInfo?: any;
    simulationResult?: CachedPluginResult;
  }) => Promise<{
    success: boolean;
    error?: string;
    toSet?: object[];       // State updates (only applied if claim succeeds)
    data?: any;
    claimNumber?: number;   // For ClaimNumbers preset
    apiCall?: {             // Outbound HTTP call to make
      uri: string;
      method: string;
      body: object;
      headers: object;
    };
  }>;
  defaultState: any;
  getPublicState: (currState: any) => PublicStateType;
  getBlankPublicState: () => PublicStateType;
}
```

The key takeaway: state updates (`toSet`) are only applied if the overall claim succeeds. This is the same atomicity guarantee your custom plugins get with BitBadges-managed state presets.
