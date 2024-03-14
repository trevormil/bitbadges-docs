# 🕵️ Authentication with Badges

Authentication is a great use case for badges. For example, grant access to this user if they own the membership badge but deny those who do not. Authentication can be facilitated in many settings (in-person, digitally, etc).&#x20;

**Authenticating with badges is two-fold.** First, you need to verify the user owns the address.  Second, you need to query ownership of the badge according to your criteria. Merely querying ownership without verifying the user's address ownership may not be sufficient, as users could potentially not own the address they say they own.&#x20;

If you simply need to query balances and not verify address ownership, you do not need badge authentication and can simply check ownership via the BitBadges site or another method.

**Step 1: Query Balances**

Balances can be verified by querying them somehow, which can be via the BitBadges website, BitBadges API, directly from source for off-chain balances, or directly from a blockchain node. You can run your own API / node or self-host balances for further decentralization and other benefits like low latency, availability, etc. Keep in mind the technical knowledge required, potential delay / lag, availability, and pros and cons for all the options.

**Step 2: Verify Address Ownership**

Verifying address ownership can be done with a cryptographic signature of a unique challenge message. We recommend Blockin; however, verifying any signature will do.&#x20;

### Sign In With BitBadges (SIWBB)

Digitally, you can authenticate users and verify badge ownership, such as badge-gating a website.&#x20;

Sign In with BitBadges is a way for any website to authenticate while outsourcing all logic to a BitBadges popup. This is similar to Sign In with Google or another big tech company, but everything uses a decentralized, open-source protocol (an extension of Sign In with Ethereum). We refer you to here for [Sign In With BitBadges Blockin](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/getting-started/sign-in-with-bitbadges) documentation.&#x20;

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### QR Codes

QR codes can be created for verification purposes when you need to pre-generate signatures for a later time. For example, you may not expect users to have wallets handy at authentication time, so you have them pre-generate a QR code instead to present. An example use case might be presenting a QR code at a ticket gate in real life. QR codes may either be one-view only or stored in your BitBadges account (under the Authentication Codes) tab, depending on what the provider wants. They can also be exported and saved using multiple methods, according to your preferences.

For implementation, we refer you to the [Sign In With BitBadges Blockin](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/getting-started/sign-in-with-bitbadges) documentation for more details.

<figure><img src="../.gitbook/assets/image (51).png" alt="" width="539"><figcaption></figcaption></figure>

### Authentication Tools

Here, we provide documentation for some common verification tools / integrations as a reference. Let us know if you want to add a tool or tutorial here. **Always use third party tools at your own risk!**&#x20;

**Spreadsheets**

Handle your authentication of QR code svia the use of Google Sheets.

{% content-ref url="../for-developers/tutorials/auth-qr-codes-sign-in-with-bitbadges/spreadsheet-session-management.md" %}
[spreadsheet-session-management.md](../for-developers/tutorials/auth-qr-codes-sign-in-with-bitbadges/spreadsheet-session-management.md)
{% endcontent-ref %}

**Blockin**

[Blockin](https://blockin-quickstart.vercel.app) is a universal, multi-chain, open-source sign-in standard built by BitBadges with native BitBadges ownership verification. Badge-gate both digitally (e.g. websites) and in-person (e.g. QR codes) with Blockin.&#x20;

Our native tools (Sign In with BitBadges and QR codes) are built with Blockin, but Blockin is a universal standard that can be customized how you would like.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (46).png" alt="" width="563"><figcaption></figcaption></figure>
