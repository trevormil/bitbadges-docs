# Testnet Faucet API

The testnet faucet provides free BADGE tokens for testing. No API key is required on testnet, and CORS restrictions are relaxed.

## Endpoint

```
POST https://api.bitbadges.io/testnet/api/v0/faucet
```

## Request

```json
{
  "address": "bb1..."
}
```

The address must be in BitBadges (`bb1...`) format. Use the SDK's `convertToCosmosAddress()` to convert from Ethereum or other formats if needed.

## Response

**Success (200):** Empty response body. Tokens are queued for delivery and will arrive shortly.

**Error (500):** JSON error response. Common errors:

| Error | Cause |
|-------|-------|
| `Already_airdropped` | This address has already received faucet tokens |
| `Invalid_request._Origin_not_found.` | Could not determine request origin |

## Behavior

- **Amount:** 1000 `ubadge` per request
- **Limit:** 1 airdrop per address (lifetime)
- **Processing:** Asynchronous queue - tokens may take a few seconds to arrive
- **Authentication:** None required on testnet

## Examples

### cURL

```bash
curl -X POST https://api.bitbadges.io/testnet/api/v0/faucet \
  -H "Content-Type: application/json" \
  -d '{"address": "bb1abc123..."}'
```

### SDK (TypeScript)

```typescript
// Using fetch directly
const response = await fetch('https://api.bitbadges.io/testnet/api/v0/faucet', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: 'bb1abc123...' })
});

if (!response.ok) {
  const error = await response.json();
  console.error('Faucet error:', error);
}
```

### Bot Pattern

```typescript
import { GenericCosmosAdapter } from 'bitbadgesjs-sdk';

// Generate a new bot wallet
const adapter = await GenericCosmosAdapter.fromMnemonic(
  process.env.BOT_MNEMONIC!,
  'bitbadges-2'
);

// Fund it (one-time)
await fetch('https://api.bitbadges.io/testnet/api/v0/faucet', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: adapter.address })
});

// Wait briefly for tokens to arrive
await new Promise(r => setTimeout(r, 5000));

// Now ready to sign and broadcast transactions
```

## Notes

- The faucet is testnet-only. On mainnet, acquire BADGE through normal channels.
- Each address can only use the faucet once. If you need more tokens, use a different address or contact the team on Discord.
- See [Testnet Mode](../bitbadges-blockchain/testnet-mode.md) for full testnet environment details.
