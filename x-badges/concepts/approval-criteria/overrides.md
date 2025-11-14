# Override User Level Approvals

Collection-level approvals can override user-level approvals to force transfers.

## Interface

```typescript
interface ApprovalCriteria<T extends NumberType> {
    overridesFromOutgoingApprovals?: boolean;
    overridesToIncomingApprovals?: boolean;
}
```

## How It Works

-   **`overridesFromOutgoingApprovals: true`**: Skip sender's outgoing approvals
-   **`overridesToIncomingApprovals: true`**: Skip recipient's incoming approvals

This enables forced transfers without user consent.

## Use Cases

-   **Force Revoke**: Remove tokens from users
-   **Freeze Tokens**: Prevent transfers regardless of user settings
-   **Emergency Actions**: Administrative control over transfers

## Mint Address Requirement

**CRITICAL**: Mint address approvals must always override outgoing approvals:

```json
{
    "fromListId": "Mint",
    "approvalCriteria": {
        "overridesFromOutgoingApprovals": true
    }
}
```

The Mint address has no user-level approvals, so overrides are required for functionality.

## Dangerous Configuration Warning

⚠️ **Dangerous Configuration Detected**

When an approval enables forceful transfers without a non-Mint address sender consent (via `overridesFromOutgoingApprovals: true`), this can cause dangerous side effects if not handled properly.

### Risks

Forceful transfers can break protocols that rely on escrow or alternate state. Ensure you understand all side effects of every initiated transfer before proceeding.

**Example**: Revoking from liquidity pools without handling pool shares can desync balances and enable exploits like infinite glitches (e.g. repeated revoke → rejoin pool → infinite liquidity).

### Recommendations

1. **Educate All Potential Initiators**

    - Ensure all approved initiators understand the risks and will handle transfers responsibly.

2. **Use Multi-Sig**

    - Use multi-sig or governance addresses to prevent single points of failure and unauthorized use.

3. **Consider Alternatives**
    - Do you really need revocation capabilities? If not, consider using safer approaches like whitelists, ownership checks, or other freezing methods instead of revoking to avoid these risks entirely.

## Reserved Addresses Protection

BitBadges reserves certain addresses that cannot be forcefully overridden as a sanity check. This protection is **not a replacement for due diligence** but serves as an additional safety measure to prevent accidental or malicious transfers from critical protocol addresses.

### Protected Address Types

The following addresses are protected from forceful overrides:

1. **Liquidity Pool Addresses**

    - Every liquidity pool address is protected to prevent desyncing balances and enabling exploits

2. **Cosmos Coin Wrapper Path Addresses**

    - Every cosmos coin wrapper path address is protected to maintain wrapper functionality integrity

3. **Governance-Configured Addresses**
    - Any addresses explicitly set by governance as protected addresses

### Important Notes

-   This protection is a **sanity check**, not a comprehensive security measure
-   You should still perform thorough due diligence when designing approvals and initializing forceful transfers
-   The protection applies to forceful transfers (when `overridesFromOutgoingApprovals: true` is used)
-   This does not apply to transfers to / from these addresses that actually check their user-level approvals.
-   We bypass these protections if the initiatedBy === from address (the initiator is the protected address)
-   Normal transfers (with user consent) are not affected by this protection
