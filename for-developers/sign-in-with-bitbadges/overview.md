# Overview

Authentication becomes seamless with BitBadges, offering a unified OAuth 2.0 interface across different blockchain ecosystems. Instead of managing multiple interfaces, BitBadges allows you to authenticate users from any chain, verify attestation signatures, and verify ownership of badges, NFTs, and more. This documentation will guide you through utilizing our authentication tools effectively.

This is the all-in-one flow for authentication and authorization. We envision most use cases will just want to authenticate users for their application; however, this flow also supports authorizing scopes for the BItBadges API.

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

**Authorization URL:** https://bitbadges.io/siwbb/authorize

**Token URL:** https://api.bitbadges.io/api/v0/siwbb/token

**Revoke URL:** https://api.bitbadges.io/api/v0/siwbb/token/revoke

## Quickstart

As you read along, you can refer to the [BitBadges quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) to get started and as an implementation reference. This has a full end-to-end implementation of Sign In with BitBadges.

## P2P Verification

Before diving in, we want to preface by saying that the Peer Verification feature that is natively integrated into the BitBadges site may be enough for you. Consider the best approach for your use case.

Peer Verification lets a prover device (which is signed in to a BitBadges account) prove their signed in address to a verifier device (no login required) and potentially ownership of badges / attestations all directly via the site.

Pros: No setup required. Accessible on the go. All directly in-site.

Cons: No backend server which means no session handling, custom functionality, automatic handling, etc. Requires two way connection. Both devices must be online and prover must be signed in. A connection must also be established.

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

## **Execution Flow**

**Scope of SIWBB**

Think of SIWBB as outsourcing the core username / password step with a cryptographic signature plus additional verification criteria (like verifying badges or user attestations). Pretty much, you can use our libraries, tools, and user interface to help you build your set of criteria for verification and handle the verification logic. This can include the following

* Prompting and verifying users for proof of address
* Querying asset ownership (badges, address lists, NFTs on other chains)
* Verify attestation signatures from other issuers
* Check sign-ins of other socials (Discord, GitHub, Google, X)

We leave the rest up to you. SIWBB does not handle sessions, etc. Those are all to be implemented alongside SIWBB using industry standards such as JWTs, or whatever method you prefer.

**Is the flow OAuth 2.0 compatible?**

Yes, we are OAuth 2.0 compatible and integrate with different OAuth 2.0 helper tools.

**Key Parts**

BitBadges authentication is structured into three key components: verifying address ownership, verifying asset ownership, and verifying attestations or off-chain signatures. Depending on your requirements, you can tailor your implementation by utilizing one or more of these components. We aim to provide maximum flexibility in the design process.

Our primary implementation, "Sign In with BitBadges" and "Blockin," encompasses support for all three components within a unified interface. However, you have the freedom to customize and integrate these components according to your needs, allowing for a tailored authentication solution.

#### Execution Flow:

1. **User Interaction:**
   * Users access a personalized BitBadges URL, either directly or through a popup window. At this URL, they prove address ownership, generating a unique authorization code.
2. **Authentication Details Retrieval:**
   * Authentication details can be obtained either through a callback from the popup window or retrieved from the user's BitBadges account, specifically under the "Authentication Codes" tab, using the associated code ID.
3. **Verification Process:**
   * At verification time, which may be immediate or delayed according to your implementation, utilize the BitBadges API and SDK to verify address ownership, asset ownership, and any other provided attestations.
4. **Application-Specific Logic:**
   * Implement application-specific requirements, such as session management, prevention of replay attacks, and any other custom logic necessary for your use case. This step ensures the seamless integration of BitBadges authentication into your application workflow.

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

**Verifying Address Ownership**

Verifying address ownership entails users signing in to BitBadges. We reuse the address signature from BitBadges sign ins as proof of identity / address for your application as well. The process is cost-free and doesn't involve blockchain transactions, just simple message signatures.

**Querying Asset Ownership**

The next part of authentication is to query asset ownership. We recommend outsourcing this to the BitBadges API, but you can also self-implement. The BitBadges API currently supports all BitBadges assets, as well as other chains NFTs (Ethereum, Polygon, Solana) which are in beta mode.

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

**Verifying Attestations / Signatures**

Lastly, you may also have additional attestation signatures / proofs that you want to check. For example, maybe you want to verify ownership of a diploma badge but also verify a secret attestation to one's GPA signed by the university. These can be attached along with the sign in request and verified by you.

## **Digital or QR Authentication?**

Authentication can be facilitated in many settings (in-person, digitally, etc). This is an important question to ask before starting your implementation.

**Immediate (Digital)**

You can authenticate users and verify badge ownership, such as badge-gating a website. This process is immediate, meaning as soon as a user is prompted, you can proceed to the verification step. Nothing needs to be cached or stored because the details are immediately used.

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

**Delayed Authentication (QR Codes)**

Or, you can pre-generate with authentication QR codes. For example, you may not expect users to have wallets handy at authentication time, so you have them pre-generate their authentication details to present to you at authentication time. An example use case might be presenting a QR code at a ticket gate in real life.

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

## **Security Considerations**

This flow is OAuth 2.0 compatible, so we refer you to the official spec and other OAuth 2.0 resources for security considerations with the protocol itself (replay attacks, man in the middle, etc). We also refer you to the Sign In with Ethereum spec as SIWBB uses an extension of this behind the scenes for message signatures. Below, we explain some security considerations that may be unique to SIWBB or different from typical flows:

#### **Flash Ownership Attacks** <a href="#security-flash-ownership-attacks" id="security-flash-ownership-attacks"></a>

If you are authenticating with assets (e.g. verify Bob owns this asset at sign-in time), you need to have protection against what we call flash ownership attacks. This attack is where, for example, Bob signs in with Asset A and immediately transfers Asset A to Alice who then also signs in successfully with Asset A. Two sign ins were approved for Asset A when only one should have been.

Solutions can vary dependent on the application, but here are some ideas:

* Assert that the asset cannot be transferred on-chain. This can be by making it completely non-transferable or only transferable in desired ways (such as by a trusted entity).
* If assets are non-fungible, consider preventing two sign ins with the same badge

Note that for chains that support ownership times (such as BitBadges), this is not adequate since ownership times can be transferred. For example Bob signs in with Asset A (Monday - Wednesday) but then transfers the badge + rights from Monday - Wednesday to Alice.
