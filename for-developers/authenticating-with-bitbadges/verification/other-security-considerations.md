# Other Security Considerations

This flow is OAuth 2.0 compatible, so we refer you to the official spec and other OAuth 2.0 resources for security considerations with the protocol itself (replay attacks, man in the middle, etc). We also refer you to the Sign In with Ethereum spec as SIWBB uses an extension of this behind the scenes for message signatures. Below, we explain some security considerations that may be unique to SIWBB or different from typical flows:

#### **Flash Ownership Attacks** <a href="#security-flash-ownership-attacks" id="security-flash-ownership-attacks"></a>

If you are authenticating with assets (e.g. verify Bob owns this asset at sign-in time), you need to have protection against what we call flash ownership attacks. This attack is where, for example, Bob signs in with Asset A and immediately transfers Asset A to Alice who then also signs in successfully with Asset A. Two sign ins were approved for Asset A when only one should have been.

Solutions can vary dependent on the application, but here are some ideas:

* Assert that the asset cannot be transferred on-chain. This can be by making it completely non-transferable or only transferable in desired ways (such as by a trusted entity).
* If assets are non-fungible, consider preventing two sign ins with the same badge

Note that for chains that support ownership times (such as BitBadges), this is not adequate since ownership times can be transferred. For example Bob signs in with Asset A (Monday - Wednesday) but then transfers the badge + rights from Monday - Wednesday to Alice.
