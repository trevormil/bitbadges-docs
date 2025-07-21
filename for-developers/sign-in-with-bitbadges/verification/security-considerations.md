# Security Considerations

This flow is OAuth 2.0 compatible, so we refer you to the official spec and other OAuth 2.0 resources for security considerations with the protocol itself (replay attacks, man in the middle, etc). 

You may also be prone to flash criteria checks, such as flash ownership checks. For example, Bob signs in, transfers a badge to Alice, and Alice signs in with the same badge. Both are authenticated with the same badge which may not be desired. This can apply to any criteria not just badges. Ensure your claim criteria, attestations, etc protect against such cases.