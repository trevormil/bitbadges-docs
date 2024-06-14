# Overview

Authentication becomes seamless with BitBadges, offering a unified OAuth 2.0 interface across different blockchain ecosystems. Instead of managing multiple interfaces, BitBadges allows you to authenticate users from any chain, verify attestation signatures, and verify ownership of badges, NFTs, and more. This documentation will guide you through utilizing our authentication tools effectively.

Note that this is different from the BitBadges API authorization. Sign In with BitBadges aims to be a way to authenticate users for YOUR application. It does not grant access to any gated API requests on behalf of the user. Thus, no access tokens are obtainable or used in this flow.

#### Example Use Cases:

**In-Person QR Codes:**

In scenarios where verifying users via a QR code is preferable to signing a message with a crypto wallet, BitBadges simplifies the process. For instance, during in-person events, expecting users to have their crypto wallets readily available for signing authentication messages may not be practical. In such cases, users can sign their authentication messages beforehand, enabling the generation of a QR code (or alternative methods like NFC) through BitBadges. This QR code can then be presented during authentication, streamlining the process.

**Sign In with BitBadges Popup Window:**

Similar to how websites implement authentication popups, BitBadges facilitates a streamlined approach with "Sign In with BitBadges." This feature allows users to authenticate seamlessly without leaving the current context, enhancing user experience and security.

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

## Quickstart

As you read along, you can refer to the [BitBadges quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) to get started and as an implementation reference. This has a full end-to-end implementation of Sign In with BitBadges.

<figure><img src="../../.gitbook/assets/image (80).png" alt="" width="563"><figcaption></figcaption></figure>

## Alternatives

Before diving in, we want to preface by saying that Sign In with BitBadges may be overkill for many use cases. The Peer Verification feature that is natively integrated into the BitBadges site may be enough for you. Consider the best approach for your use case.

Peer Verification lets a prover device (which is signed in to a BitBadges account) prove their signed in address to a verifier device (no login required) and potentially ownership of badges all directly via the site. This can establish proof of address because BitBadges accounts require proof of address for authentication in the first place. So for example, you, as a verifier, might verify proof of address ownership then view their BitBadges profile to verify badge ownership.

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

While this is easy to use and has no setup required, its functionality is limited:

* Non-customizable: Everything is directly in-site. You do not have any backend server setup for custom functionality or automatic processing. This also means session handling is not possible.
* Only supports proof of address and checking badge ownership. It doesn't support native SIWBB features like other tracked sign-ins, sessions, and attestations.
* Requires two-way connection: Both devices must be online, and there is a handshake process to initiate the websocket connection. The prover must also be signed in at authentication time. This is as opposed to SIWBB QR codes where the prover can present a pre-generated QR code in an offline setting without signing in.

## **Execution Flow**

**Scope of SIWBB**

Think of SIWBB as outsourcing the core username / password step with a cryptographic signature plus additional verification criteria (like verifying badges or user attestations). Pretty much, you can use our libraries, tools, and user interface to help you build your set of criteria for verification and handle the verification logic. This can include the following

* Prompting and verifying users for proof of address
* Querying asset ownership (badges, address lists, NFTs on other chains)
* Verify attestation signatures from other issuers
* Check sign-ins of other socials (Discord, GitHub, Google, X)

We leave the rest up to you. SIWBB does not handle sessions, etc. Those are all to be implemented alongside SIWBB using industry standards such as JWTs, or whatever method you prefer.

**Is the flow OAuth 2.0 compatible?**

Yes, we are OAuth 2.0 compatible and integrate with different OAuth 2.0 helper tools.&#x20;

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

The next part of authentication is to query asset ownership. Balances can be verified by querying them somehow, which can be via the BitBadges website, BitBadges API, directly from source for off-chain balances, or directly from a blockchain node. You can run your own API / node or self-host balances for further decentralization and other benefits like low latency, availability, etc. Keep in mind the technical knowledge required, potential delay / lag, availability, and pros and cons for all the options.

The BitBadges API currently supports all BitBadges assets, as well as Ethereum / Polygon NFTs.

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

**Verifying Attestations / Signatures**

Lastly, you may also have additional attestation signatures / proofs that you want to check. For example, maybe you want to verify ownership of a diploma badge but also verify a secret attestation to one's GPA signed by the university. These can be attached along with the sign in request.

## **Immediate or Delayed Authentication?**

Authentication can be facilitated in many settings (in-person, digitally, etc). This is an important question to ask before starting your implementation.

**Immediate**

You can authenticate users and verify badge ownership, such as badge-gating a website. This process is immediate, meaning as soon as a user is prompted, you can proceed to the verification step. Nothing needs to be cached or stored because the details are immediately used.

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

**Delayed Authentication**

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
