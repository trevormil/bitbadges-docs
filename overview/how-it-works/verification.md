# Verification

### Querying Badge Ownership

Querying badge ownership involves checking who owns badges at specific times. This process allows you to gather information about badge holders.

In the context of BitBadges, querying badge ownership can be performed through the web app or various alternative methods such as running your own blockchain or utilizing community-built tools tailored to different use cases (websites, in-person events, etc). You can explore these tools in the [Ecosystem](../ecosystem/) section and choose the one that best suits your requirements.

### Verifying Badge Ownership

Once you create and distribute your badges, a big part of offering utility is verifying who owns your badges at specific times. **Important: Badge verification is a two-step process:**

* Querying badge ownership - Check who actually owns the badge
* Verification of address ownership - Verify the user requesting authentication actually owns the address they are claiming

Merely checking the BitBadges website ([https://bitbadges.io](https://bitbadges.io/)) without verifying the user's address ownership may not be sufficient, as users could potentially not own the address they say they own. We recommend using Blockin to perform verification. For implementing this, see the developer documentation.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

Blockin can be used to implement both digital and in-person verificatin.

#### Digital Verification

For digital verifiation (such as badge-gating websites), you may see a modal such as the one below, which walks you through all details about the sign in request and badge ownership requirements.

#### In-Person Verification

&#x20;In-person verification can be facilitated through pre-signing a message and presenting the signature at authentication time (typically through a QR code as shown below). This is typically handled via the Authentication QR Codes tab on the website.



<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="563"><figcaption></figcaption></figure>
