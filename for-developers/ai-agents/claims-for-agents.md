# Claims for Agents

Claims are BitBadges' flexible criteria engine. For agents, claims enable gated minting and transfers where off-chain criteria (codes, passwords, ownership checks, custom logic) control on-chain token operations. The core flow is: **create claim -> complete claim -> get merkle code -> use merkle code in MsgTransferTokens**.

## Two Claim Flows

**Standalone claims (off-chain only):**
Criteria check -> reward. The reward is off-chain: points, address list spots, gated content access, etc. No blockchain transaction needed.

**On-chain gated claims:**
Criteria check -> reserved merkle code -> merkle proof -> on-chain transfer/mint via `MsgTransferTokens`. This is the flow agents typically need.

## Creating a Claim Programmatically

Use the BitBadges API SDK to create claims. The example below creates a code-gated claim. To link it to an on-chain collection for gated minting, associate it with a collection approval via the BitBadges UI or API — the `collectionId` and `trackerDetails` are applied automatically.

```typescript
import crypto from 'crypto';
import { BitBadgesAPI } from 'bitbadgesjs-sdk';

const api = new BitBadgesAPI({ apiUrl: 'https://api.bitbadges.io' });

// Generate codes
const seedCode = crypto.randomBytes(32).toString('hex');
const numCodes = 50;
const codes = [];
for (let i = 0; i < numCodes; i++) {
  codes.push(
    crypto.createHash('sha256').update(`${seedCode}-${i}`).digest('hex') + `-${i}`
  );
}

await api.createClaim({
  claims: [
    {
      plugins: [
        {
          pluginId: 'numUses',
          instanceId: 'num-uses',
          version: '0',
          publicParams: { maxUses: numCodes },
          privateParams: {}
        },
        {
          pluginId: 'codes',
          instanceId: 'codes-gate',
          version: '0',
          publicParams: { numCodes },
          privateParams: { codes, seedCode }
        }
      ],
      state: {},
      action: { seedCode },
      metadata: {
        name: 'My Agent Claim',
        description: 'Codes distributed by my bot'
      }
    }
  ]
});
```

If you are using the BitBadges MCP tools, use the `build_claim` tool instead of constructing the payload manually (see MCP Tool Workflow below).

## Completing a Claim

Claim completion is asynchronous. You submit the attempt, then poll for the result.

```typescript
// Submit the claim attempt
const res = await api.completeClaim(claimId, userAddress, {
  _expectedVersion: 0,
  'codes-gate': { code: codes[0] } // Plugin inputs keyed by instanceId
});

// Poll for result (~2-5 seconds)
let status;
do {
  await new Promise((r) => setTimeout(r, 2000));
  status = await api.getClaimAttemptStatus(res.claimAttemptId);
} while (!status.success && !status.error);

if (status.success) {
  console.log('Claim completed! Code:', status.code);
}
```

## The Merkle Proof Flow (On-Chain Claims)

This is the key flow agents need. After completing a claim for an on-chain collection, you must retrieve your reserved merkle code, build a proof, and submit it in a `MsgTransferTokens`.

### Step 1: Get reserved codes and leaf signatures

```typescript
const reserved = await api.getReservedClaimCodes(claimId, userAddress);
// reserved.reservedCodes = ['abc123...']
// reserved.leafSignatures = ['0x...']
```

### Step 2: Get the merkle proof path

```typescript
const proofInfo = await api.getMerkleProofInfo(
  collectionId,
  challengeTrackerId,
  reserved.reservedCodes[0]
);
// proofInfo.aunts = [{ aunt: 'hash', onRight: true }, ...]
```

### Step 3: Build and submit the transfer

```typescript
const result = await client.signAndBroadcast([
  MsgTransferTokens.create({
    creator: client.address,
    collectionId: '123',
    transfers: [
      {
        from: 'Mint',
        toAddresses: [client.address],
        balances: [
          {
            badgeIds: [{ start: '1', end: '1' }],
            amount: '1',
            ownershipTimes: [{ start: '1', end: '18446744073709551615' }]
          }
        ],
        merkleProofs: [
          {
            leaf: reserved.reservedCodes[0],
            leafSignature: reserved.leafSignatures[0],
            aunts: proofInfo.aunts
          }
        ],
        prioritizedApprovals: [
          {
            approvalId: 'mint-approval',
            approvalLevel: 'collection',
            approverAddress: '',
            version: '0'
          }
        ],
        onlyCheckPrioritizedCollectionApprovals: true,
        onlyCheckPrioritizedIncomingApprovals: false,
        onlyCheckPrioritizedOutgoingApprovals: false,
        memo: ''
      }
    ]
  })
]);
```

The `prioritizedApprovals` array tells the chain which approval to check. The `approvalId` must match the approval on the collection that references the claim's merkle challenge. Always specify `prioritizedApprovals` even if empty.

## Common Patterns for Agents

- **Bot distributes codes**: Create a code-gated claim, distribute codes to users via your app or bot, users complete the claim on BitBadges or you complete on their behalf.
- **Backend auto-completion**: Create a password-gated claim where only your backend knows the password. Complete claims on behalf of users when they meet your custom criteria.
- **Ownership-gated minting**: Use the `must-own-badges` plugin to gate minting based on existing token ownership. Users who hold token X can mint token Y.
- **Time-windowed drops**: Add `transferTimes` constraints to limit when claims can be completed.

## MCP Tool Workflow

If you are using the BitBadges MCP builder tools, the `build_claim` tool handles payload construction:

```
build_claim({
  claimType: "code-gated",
  name: "My Drop",
  maxUses: 100
})
```

The tool returns a claim payload ready for submission to the API. From there, the agent follows the same complete -> get codes -> submit proof flow described above.

## Tips

- Always call `simulateClaim` before `completeClaim` in production to catch errors without consuming a use.
- Match `numUses` to the merkle tree leaf count for on-chain claims. A mismatch means some codes won't have valid proofs.
- Use `_expectedVersion` in `completeClaim` to prevent race conditions when multiple agents or users compete for the same claim.
- Poll `getClaimAttemptStatus` after submitting. Claims process asynchronously and typically resolve in 2-5 seconds.
