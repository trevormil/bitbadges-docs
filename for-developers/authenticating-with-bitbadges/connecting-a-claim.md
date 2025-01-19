# Connecting a Claim

Sign In with BitBadges becomes super powerful when you connect a BitBadges claim because you can natively:

* Check badge ownership
* Check social sign-ins from any supported social
* Receive privately held attestations securely
* Connect to 7000+ app integration plugins
* And much more

Consider claims as "attached" or a complement to SIWBB because they are separate entities and are verified separately. But, they are often used together because to prove a successful claim, you need to both 1) authenticate (SIWBB) and 2) prove claim criteria was met (the claim attempt itself). With both, you can prove the user successfully met the criteria.&#x20;

Create claims and manage them in the developer portal separately, and see corresponding  documentation for more information on managing / verifying them. Note you can attach both on-demand claims and indexed claims. Indexed claims require a "completion" action by the user whereas on-demand is just behind the scenes.

{% content-ref url="../../overview/claim-builder/" %}
[claim-builder](../../overview/claim-builder/)
{% endcontent-ref %}





Here are a couple common approaches for the connection:

**Approach 1: Directly In SIWBB Flow**

Specify the `claimId`in the URL parameters and prompt the user on the authorize screen. Then, check successes server-side with the verified address from SIWBB.

<figure><img src="../../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

**Approach 2: Behind the Scenes**

For on-demand ones, you may not even want to display the criteria to the user. You can simply omit it from any interface and just check it server-side behind the scenes.

**Approach 3: Claim Rewards**

Consider creating a secret URL gated to successful claimees in the Rewards tab of the claim. The user would be first redirected to the SIWBB screen and eventually to your app with authentication.

<figure><img src="../../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>
