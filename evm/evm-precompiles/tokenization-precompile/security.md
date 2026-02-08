# Security

The precompile implements security measures to protect against common attack vectors.

## Security Features

### Caller Verification

All transaction methods automatically use `msg.sender` as the caller. The caller cannot be spoofed - it's always the address that directly called the contract.

### Input Validation

All inputs are validated:
- Zero addresses are rejected
- Invalid ranges (start > end) are rejected
- Array sizes are limited to prevent DoS attacks
- Business logic constraints are enforced

### DoS Protection

Array size limits prevent denial-of-service attacks:
- Maximum 100 recipients per transfer
- Maximum 100 token ID ranges
- Maximum 100 ownership time ranges

### Reentrancy Protection

Operations execute atomically through the Cosmos SDK state machine, preventing reentrancy attacks.

## Best Practices

1. **Validate inputs** in your contracts before calling the precompile
2. **Check return values** - all methods return success indicators
3. **Handle errors** appropriately using try-catch blocks
4. **Use helper library** for type construction and validation
5. **Review permissions** - ensure approvals and collection permissions are configured correctly

For detailed security implementation, see the [precompile code](https://github.com/BitBadges/bitbadgeschain/tree/evm-poc/x/tokenization/precompile).
