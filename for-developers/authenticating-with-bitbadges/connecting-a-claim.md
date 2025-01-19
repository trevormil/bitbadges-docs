# Connecting a Claim

Sign In with BitBadges becomes super powerful when you connect a BitBadges claim because you can natively:

-   Check badge ownership
-   Check social sign-ins from any supported social
-   Receive privately held attestations securely
-   Connect to 7000+ app integration plugins
-   And much more all within the same flow.

Treat claims as just "attached" and not a part of the core SIWBB process. You need to verify it separately server-side accordingly to your needs. Specifying the `claimId` in the URL parameters will display it to the user in the interface, but it does not guarantee verification. You will use the combination of 1) Sign In with BitBadges address authentication and 2) looking up the successful claim attempt server-side for that address to verify the user has satisfied the claim criteria.

Create claims and manage them in the developer portal separately, and see corresponding documentation for more information on managing / verifying them. Note you can attach both on-demand claims and indexed claims. Indexed claims require a "completion" action by the user whereas on-demand is just behind the scenes.

{% content-ref url="../../overview/claim-builder/" %}
[claim-builder](../../overview/claim-builder/)
{% endcontent-ref %}

Here are a couple common approaches for the connection:

**Approach 1: Directly In SIWBB Flow**

Specify the `claimId`in the URL parameters and prompt the user to claim or just display the criteria on the authorize screen. Then, check successes server-side with the verified address from SIWBB.

<figure><img src="../../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

**Approach 2: Behind the Scenes**

For on-demand ones, you may not even want to display the criteria to the user. You can simply omit it from any interface and just check it server-side behind the scenes.

For example, just check badge ownership behind the scenes.

**Approach 3: Claim Rewards**

Consider creating a secret URL gated to successful claimees in the Rewards tab of the claim. The user would be first redirected to the SIWBB screen and eventually to your app with authentication.

<figure><img src="../../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>
