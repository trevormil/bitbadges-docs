# Challenge Parameters

**challengeParams** are the Blockin parameters for the sign-in challenge. See here for the full type definition [https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html](https://blockin-labs.github.io/blockin/docs/interfaces/ChallengeParams.html). These will make up the base message that is expected. Here, you include all details about the sign-in like expiration date, badges to be owned, etc.

If **allowAddressSelect** is true, we will handle the address selection on our end. Meaning, we override challengeParams.address and challengeParams.chain with the user's selected address / chain, respectively. If false, we enforce that the connected address is exactly what is specified in **challengeParams**.
