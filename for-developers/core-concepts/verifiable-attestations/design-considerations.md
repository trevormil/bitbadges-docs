# Design Considerations

Attestations are super open-ended with many different approaches with varying tradeoffs. Below, we outline some additional design considerations or things you may want to think of.&#x20;

## **Badges with Attestations**

Leveraging on-chain badges or tokens could be useful to you if you need stateful data, a ledger of activity, transferability requirements, and more. For example,

-   To "have the credential", you must prove ownership of the badge and the credential. This can be used in cases where the credential itself can be public and is to be displayed in a portfolio (BitBadges) but has certain aspects that may need to maintain private (attestations). The design also may enable credentials to be transferable.
-   Badges can also be used for on-chain, tamper-proof, decentralized revocation or suspension statuses. In the credential somewhere, you say that this badge must not be burned or revoken on-chain for it to be valid.

### Malicious Issuers

All attestations / credentials inherently get their credibility from the issuer, so there is already a bit of trust there. However, additional measures can be taken to protect against a malicious issuer. Some examples include:

-   On-chain ID -> data integrity maps to prevent issuer from issuing duplicates (each credential ID can only correspond to one credential)
-   Anchors / Data Commitments - The issuer or holder can commit to proof of knowledge on-chain at some point which can be verified later. This gives a verifiable timestamp for when the data was known by. See below for more info.

### Custom Logic

It is important to note that proof verification is not limited to that is provided in the interface, you will typically also need to check the attestation messages are as expected against other private values (e.g. matching attestation data to a user). A valid proof is not sufficient if the data is not as expected. This is application-specific, but we expect you to handle everything for proper verification.

### Replay Attacks

Signatures are static, always verifiable, and not "revokable". If a signature gets in a malicious parties' hands, the signature will still be valid. There are many things that can be done to help mitigate this:

-   Revocation registries
-   Authenticating the holder at verification time
-   And many more approaches

### On-Chain Anchors + Update History

While a cryptographic signature proves data integrity / proof of knowledge, you may also need to verify knowledge or integrity with timestamps. For this, consider creating on-chain anchor transactions which can be used for such purposes. Anchors do not necessarily have to reveal private information. Posting a hash, encryption, etc is sufficient as long as it can prove what you need it to prove.

For BitBadges, anchors are facilitated through the x/anchor module (MsgAddCustomData). It is a very simple module that allows you to store arbitrary strings.

If an anchor is created through the BitBadges site, we use the following algorithm. If we have N attestation messages, we also have N entropies. We can then post the SHA256 hash of (message + entropy) on-chain. When revealed to a verifier in a proof, they can verify that data integrity has been maintained if they have the palintext message + entropy value. The random hash posted on-chain reveals nothing confidential but provides a verifiable timestamp for the data. This is facilitated through the x/anchor module's MsgAddCustomData.

```typescript
{
    type: 'MsgAddCustomData',
    msg: {
      data: JSON.stringify(
        attestation.messages.map((message, idx) => {
          return CryptoJS.SHA256(message + entropies[idx]).toString();
        })
      )
    }
}
```

Update history is also maintained by BItBadges in a centralized manner, but anchors could be useful to provide additional information to verifiers about when the data changed, verifiable timestamps, etc.

Other approaches like WITNESS proofs and many others are available. These just need to go on a trusted blockchain (not necessarily BitBadges blockchain).
