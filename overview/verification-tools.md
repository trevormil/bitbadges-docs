# 🕵 Authentication with Badges

Authentication is a common use case for badges. For example, grant access to this user if they own the membership badge but deny those who do not. Authentication can be done in many ways (in-person, digitally, etc).

**Authenticating with badges is two-fold.** First, you need to verify the user owns the address.  Second, you need to query ownership of the badge according to your criteria. Merely querying ownership without verifying the user's address ownership may not be sufficient, as users could potentially not own the address they say they own.&#x20;

If you simply need to query balances and not verify address ownership, you do not need badge authentication and can simply query ownership via the BitBadges site or another method.

**Step 1: Query Balances**

Balances can be verified by querying them somehow, which can be via the BitBadges website, BitBadges API, directly from source for off-chain balances, or directly from a blockchain node. You can run your own API / node or self-host balances for further decentralization and other benefits like low latency, availability, etc. Keep in mind the potential delay / lag, availability, and pros and cons for all the options .

**Step 2: Verify Address Ownership**

Verifying address ownership can be done with a cryptographic signature. As mentioned above, we recommend Blockin; however, any cryptographic signature will do.&#x20;

### Authentication Tools

Here, we provide documentation for some common verification tools / integrations as a reference. Let us know if you want to add a tool or tutorial here. **Always use third party tools at your own risk!**&#x20;

We recommend using [Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/) for verification. [Blockin](https://blockin-quickstart.vercel.app) is a universal, multi-chain sign-in standard built by BitBadges with native BitBadges ownership verification. Badge-gate both digitally (e.g. websites) and in-person (e.g. QR codes) with Blockin. See the Blockin documentation for implementation.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (46).png" alt="" width="563"><figcaption></figcaption></figure>
