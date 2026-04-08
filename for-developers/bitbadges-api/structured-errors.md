# Structured Error Messages

When a transaction fails during broadcast or simulation, the API returns structured error information that categorizes the failure and suggests a fix.

## Error Response Format

Failed broadcast and simulate responses include a `structuredError` object in the developer message:

```json
{
  "error": "The transfer does not meet the required approval criteria.",
  "developerMessage": {
    "structuredError": {
      "code": "APPROVAL_CRITERIA_NOT_MET",
      "message": "The transfer does not meet the required approval criteria.",
      "category": "approval",
      "suggestion": "Check that the sender, recipient, badge IDs, and amounts match an existing approval on the collection."
    },
    "rawError": "approval criteria not met for transfer from bb1... to bb1..."
  }
}
```

## Fields

| Field | Type | Description |
|---|---|---|
| `code` | string | Machine-readable error code (e.g., `APPROVAL_CRITERIA_NOT_MET`) |
| `message` | string | Human-readable error description |
| `category` | string | Error category (see below) |
| `suggestion` | string | Actionable fix guidance |

## Categories

| Category | Description |
|---|---|
| `approval` | Transfer does not satisfy approval criteria, tracker limits exceeded, invalid proofs |
| `permission` | Action blocked by locked or frozen permissions |
| `balance` | Insufficient token balance or transaction fee |
| `validation` | Invalid addresses, badge IDs, time ranges, or other structural issues |
| `gas` | Gas estimation failed or transaction ran out of gas |
| `auth` | Unauthorized signer, account not found, sequence mismatch |
| `unknown` | Error does not match any known pattern |

## Error Code Reference

### Approval Errors

| Code | Description |
|---|---|
| `APPROVAL_CRITERIA_NOT_MET` | Transfer does not match any approval on the collection |
| `TRACKER_LIMIT_EXCEEDED` | Approval tracker cap (uses or amount) has been reached |
| `INVALID_CHALLENGE_PROOF` | Merkle proof or challenge code is invalid |

### Balance Errors

| Code | Description |
|---|---|
| `INSUFFICIENT_BALANCE` | Account does not have enough tokens for the transfer |
| `INSUFFICIENT_FEE` | Transaction fee is too low |

### Permission Errors

| Code | Description |
|---|---|
| `PERMISSION_DENIED` | Action blocked by a permanently locked permission |
| `UPDATE_NOT_ALLOWED` | Collection cannot be updated in the requested way |

### Validation Errors

| Code | Description |
|---|---|
| `INVALID_ADDRESS` | Address is not valid Cosmos (bech32) or Ethereum (0x) format |
| `INVALID_BADGE_ID` | Badge ID is outside the collection's valid token ID range |
| `INVALID_TIME_RANGE` | Time range is invalid or overlapping |
| `SLIPPAGE_EXCEEDED` | Swap slippage tolerance was exceeded |

### Gas Errors

| Code | Description |
|---|---|
| `GAS_ESTIMATION_FAILED` | Gas estimation failed or transaction ran out of gas |

### Auth Errors

| Code | Description |
|---|---|
| `UNAUTHORIZED` | Signer is not authorized for this action |
| `ACCOUNT_ERROR` | Account not found on-chain or sequence mismatch |
| `2FA_OWNERSHIP_REQUIRED` | Transaction requires badge ownership for 2FA |

## Handling in Code

### SDK

```typescript
const result = await client.signAndBroadcast(messages);
if (!result.success) {
  // result.error contains the structured message
  // result.rawResponse contains the full response with developerMessage
  console.error(result.error);
}
```

### Raw API

When calling `/api/v0/broadcast` or `/api/v0/simulate` directly, check the HTTP status code. On failure (500), the response body contains the structured error.

### CLI

```bash
bitbadges-cli api broadcast-tx --body @tx.json
# Errors are printed with the structured message and suggestion
```

## Error Tidying

The API automatically cleans up raw chain errors before returning them:
- File paths and Go stack traces are stripped
- Gas usage details are removed
- IBC denom amounts are converted to human-readable symbols (e.g., `10000000000ubadge` becomes `10 $BADGE`)
- Typia validation prefixes are removed
