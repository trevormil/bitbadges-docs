# Verifiable Secrets

In addition to the public (on-chain) badge interface, the BitBadges site supports storing off-chain verifiable secrets. These can be credentials, attestations, anything.

How it works is that the issuer will sign some private data as a message, and it will be stored entirely off-chain in their BitBadges account. They can anchor it on-chain (without revealing the data) for data commitment and time verification purposes, but this is not needed in all cases.

The issuer then gives that signature + private data to a holder or recipient (e.g. a university giving a student a diploma). The holder now has proof of this credential and can disclose it to any veriifers (who also have cryptographic proof that the data is valid). The issuer can also **selectively disclose** the minimum amount of data required (e.g. can reveal GPA > 3.0 w/o revealing other identifying information).

This presentation step to a verifier can be done manually or in-person. Typically, in the BitBadges ecosystem, you may see it attached to the sign-in flow.
