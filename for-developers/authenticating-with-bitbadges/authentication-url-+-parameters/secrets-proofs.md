# Secrets Proofs

```typescript
const popupParams = {
    ...,
    expectSecretsProofs: true,
    onlyProofs: false
}
```

**expectSecretsProofs** will tell us whether you expect the user to provide additional proof of credentials (i.e. saved secrets in their BitBadges account) to be verified. Note this will require the user to be signed in to BitBadges to fetch the proofs.

**onlyProofs** means you do not even expect a signature to be presented (only using the UI for obtaining proofs). See [here](https://docs.bitbadges.io/for-developers/core-concepts/verifiable-secrets) for verifying proofs.
