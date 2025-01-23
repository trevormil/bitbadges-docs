# Utility Flows

Checking criteria is pointless without a reward or utility offered to those who successfully "claim". The utility or rewards is left super open-ended for you to custom implement the utility for your app.&#x20;

In some cases, the end utility may be directly on the BitBadges site:

* Badge / List Distribution: Gate badge distribution or address lists to those who claim.
* Points / Tiers: Gate points or tiers to claimers
* URLs / Content: In the rewards section, you may also find that some URLs / content can also be gated in-site. This can be anything from an attestation invite code to a Google Docs invite link to a generic website.

In other cases, the end utility can be more custom implemented by you or the service provider.

* Gated access to a website, in-person event, or something else
* Literally anything you can think of!

You may even often have multi-level flows. For example, use one claim to gate badge distribution, and use another that checks ownership for that badge and gates something else like points.

One important thing to consider is that utility completion and the claim are often separate actions that, if misconfigured, may not be aligned correctly. For example, the eventual on-chain badge transfer transaction depends on a successful claim, but there may be a case if misconfigured that the claim can be completed but the transaction cannot.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

