# Overview

Authentication becomes seamless with BitBadges, offering a unified interface across different blockchain ecosystems. Instead of managing multiple interfaces, BitBadges allows you to authenticate users from any chain, verify secret signatures, and verify ownership of badges, NFTs, and more. This documentation will guide you through utilizing our authentication tools effectively.

#### Example Use Cases:

**In-Person QR Codes:**

In scenarios where verifying users via a QR code is preferable to signing a message with a crypto wallet, BitBadges simplifies the process. For instance, during in-person events, expecting users to have their crypto wallets readily available for signing authentication messages may not be practical. In such cases, users can sign their authentication messages beforehand, enabling the generation of a QR code (or alternative methods like NFC) through BitBadges. This QR code can then be presented during authentication, streamlining the process.

**Sign In with BitBadges Popup Window:**

Similar to how websites implement authentication popups, BitBadges facilitates a streamlined approach with "Sign In with BitBadges." This feature allows users to authenticate seamlessly without leaving the current context, enhancing user experience and security.

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

## Quickstart

As you read along, you can refer to the [BitBadges quickstart repo](https://github.com/BitBadges/bitbadges-quickstart) to get started and as an implementation reference. This has a full end-to-end implementation of Sign In with BitBadges.

<figure><img src="../../.gitbook/assets/image (80).png" alt="" width="563"><figcaption></figcaption></figure>

## **Execution Flow**&#x20;

**Scope of SIWBB**

Think of SIWBB as outsourcing the core username / password step with a cryptographic signature plus additional verification criteria (like verifying badges or user secrets).  Pretty much, you can use our libraries, tools, and user interface to help you build your set of criteria for verification and handle the verification logic. This can include the following

* Prompting and verifying challenge message signatures from users
* Querying asset ownership (badges, address lists, NFTs on other chains)
* Verify secret signatures from other issuers (e.g. attestations or credentials)
* Check sign-ins of other socials (Discord, GitHub, Google, X)

Once the above is checked, the user is authenticated, excluding any other self-implemented requirements. From here, we leave the rest up to you. SIWBB does not handle sessions, authorizations, etc. Those are all to be implemented alongside SIWBB using industry standards such as OAuth 2.0, JWTs, or whatever method you prefer.

**Why does the flow look similar to authorization like OAuth 2.0?**

Our suite of tools helps you outsource the authentication step to our user interface through a similar process to popular authorization implementations (e.g. OAuth 2.0 with Sign In with Discord). We leverage many of the same techniques.&#x20;

However, the difference is that instead of generating and providing access tokens that can be used, we provide the interface for users to sign challenges and provide you with the (message, signature) pairs along with other accompanying details to use and verify.

**Key Parts**

BitBadges authentication is structured into three key components: verifying address ownership, verifying asset ownership, and verifying secrets or off-chain signatures. Depending on your requirements, you can tailor your implementation by utilizing one or more of these components. We aim to provide maximum flexibility in the design process.

Our primary implementation, "Sign In with BitBadges" and "Blockin," encompasses support for all three components within a unified interface. However, you have the freedom to customize and integrate these components according to your needs, allowing for a tailored authentication solution.

#### Execution Flow:

1. **User Interaction:**
   * Users access a personalized BitBadges URL, either directly or through a popup window. At this URL, they sign the authentication message, generating a unique authentication code (their signature).
2. **Authentication Details Retrieval:**
   * Authentication details can be obtained either through a callback from the popup window or retrieved from the user's BitBadges account, specifically under the "Authentication Codes" tab, using the associated code ID.&#x20;
3. **Verification Process:**
   * At verification time, which may be immediate or delayed according to your implementation, utilize the BitBadges API and SDK to verify address ownership, asset ownership, and any other provided secrets.
4. **Application-Specific Logic:**
   * Implement application-specific requirements, such as session management, prevention of replay attacks, and any other custom logic necessary for your use case. This step ensures the seamless integration of BitBadges authentication into your application workflow.



<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

**Verifying Address Ownership**&#x20;

Verifying address ownership entails users signing a unique challenge message, typically generated by the resource provider using Blockin (our implementation). This message, which can be modified by the user before submission, is validated by the provider to ensure its integrity. By verifying this signature, ownership of the claimed address is confirmed. The process is cost-free, doesn't involve blockchain transactions, and supports offline operations.

The challenge message can be anything as long as replay attacks can be prevented (see security section below). We use and recommend Blockin (our multi-chain sign-in standard library that extends[`EIP-4361 Sign-In With Ethereum`](https://eips.ethereum.org/EIPS/eip-4361) interface (see [https://login.xyz/](https://login.xyz/)) to support a) specifying assets (such as NFTs or badges) and b) each chain's native signature algorithm. Blockin messages outline the authentication request information in a human-readable manner.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

<pre><code><strong>https://bitbadges.io wants you to sign in with your Ethereum account:
</strong>0xb48B65D09aaCe9d3EBDE4De409Ef18556eb53085

Sign this message only if prompted by a trusted party. The signature of this message can be used to authenticate you on BitBadges. By signing, you agree to the BitBadges privacy policy and terms of service.

URI: https://bitbadges.io
Version: 1
Chain ID: 1
Nonce: cPW1vKj0xfTFlrUab
Issued At: 2024-01-21T18:35:21.141Z
Expiration Time: 2024-02-04T16:11:29.880Z
Resources:
Asset Ownership Requirements:
- Requirement A1-1 (satisfied if one of B1 is satisfied):
  - Requirement B1-1:
      Chain: BitBadges
      Collection ID: 1
      Asset IDs: 8 to 8
      Ownership Time: Authentication Time
      Ownership Amount: x1

  - Requirement B1-2:
      Chain: BitBadges
      Collection ID: 1
      Asset IDs: 9 to 9
      Ownership Time: Authentication Time
      Ownership Amount: x1
</code></pre>

**Querying Asset Ownership**

The next part of authentication is to query asset ownership. Balances can be verified by querying them somehow, which can be via the BitBadges website, BitBadges API, directly from source for off-chain balances, or directly from a blockchain node. You can run your own API / node or self-host balances for further decentralization and other benefits like low latency, availability, etc. Keep in mind the technical knowledge required, potential delay / lag, availability, and pros and cons for all the options.

The BitBadges API currently supports all BitBadges assets, as well as Ethereum / Polygon NFTs.

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

**Verifying Secrets / Signatures**

Lastly, you may also have additional secret signatures / proofs that you want to check. For example, maybe you want to verify ownership of a diploma badge but also verify a secret attestation to one's GPA signed by the university.

## **Immediate or Delayed Authentication?**

Authentication can be facilitated in many settings (in-person, digitally, etc). This is an important question to ask before starting your implementation.

**Immediate**

You can authenticate users and verify badge ownership, such as badge-gating a website. This process is immediate, meaning as soon as a user is prompted, you can proceed to the verification step. Nothing needs to be cached or stored because the details are immediately used.

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

**Delayed Authentication**

Or, you can pre-generate with authentication codes. For example, you may not expect users to have wallets handy at authentication time, so you have them pre-generate their authentication details to present to you at authentication time. An example use case might be presenting a QR code at a ticket gate in real life.&#x20;

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>



## **Security Considerations**

### **Replay Attacks** <a href="#security-replay-attacks" id="security-replay-attacks"></a>

**What are replay attacks?**

Preventing replay attacks is crucial in any authentication or authorization system. A replay attack occurs when an attacker intercepts and maliciously reuses a valid communication or transaction between two parties. In the context of Blockin, this could involve intercepting and reusing a valid authentication request (signature / code), compromising the security of the authentication process.

Lets say Bob authenticates at Event A, but his signature / code is maliciously intercepted. The adversary could now theoretically authenticate as Bob at Event A. Thus, the adversary could not **replay** the signature to be authenticated. It will be caught as already used.

Also, note that the same message signed by the same address will ALWAYS produce the same signature. Thus, it is important for your authentication message to have some sort of randomness to produce unique signatures.

Lets say Event B uses the same authentication message at Event A. Bob authenticates at Event A as intended. However, the authentication provider of Event A now has Bob's signature and could theoretically use it to authenticate at Event B as Bob. Even if the authentication is one-time use only at both Event A and Event B, it could be replayed across events.

**How can you protect?**

Please make sure you protect accordingly. **As mentioned above, you should assume the message may have been manipulated by the user before being returned to you.** This means that a replay attack mechanism must consider this as well.

Below are some ways to protect:

* Restrict and check one use per session per address
* If using assets, restrict and check one use per session per asset
* Unique nonce generation: Including a unique and random nonce (number used once) in each challenge to ensure that each request is unique, one time use only, and cannot be replayed. Nonces should be marked as used / not used by you. The generation scheme is left up to you.
* Time-dependent windows: Consider implementing a small time window where the sign in can be "redeemed". This is different from the authenticated times. For example, you have 1 minute to "redeem" your sign in after it is signed. After that, it is invalid, thus preventing future replay attacks. **However, replay attacks during the time window are still possible. This may not be applicable for some applications.**
  * These can be implemented with **issuedAt** timestamps, system times, using recent block hashes, or any time-dependent property.
  * This is typically fine for digital applications with secure, encrypted network communication

### **Flash Ownership Attacks** <a href="#security-flash-ownership-attacks" id="security-flash-ownership-attacks"></a>

If you are authenticating with assets (e.g. verify Bob owns this asset at sign-in time), you need to have protection against what we call flash ownership attacks. This attack is where, for example, Bob signs in with Asset A and immediately transfers Asset A to Alice who then also signs in successfully with Asset A. Two sign ins were approved for Asset A when only one should have been.

Solutions can vary dependent on the application, but here are some ideas:

* Assert that the asset cannot be transferred on-chain. This can be by making it completely non-transferable or only transferable in desired ways (such as by a trusted entity).
* If assets are non-fungible, consider preventing two sign ins with the same badge

Note that for chains that support ownership times (such as BitBadges), this is not adequate since ownership times can be transferred. For example Bob signs in with Asset A (Monday - Wednesday) but then transfers the badge + rights from Monday - Wednesday to Alice.