# Implementing Custom Utility

Need to perform some additional action upon the user claiming successfully?

## **Integrations**

Some integrations may check claims automatically for you. For example, create a WordPress gated site by just entering the claim ID to check in the settings.

## **In-Site Rewards - URLs / Content**

When creating rewards on the claim builder page, you can also link gated content / URLs to only be visible to users upon successfully claiming.

You may want additional authentication depending on your tolerance level. You can even consider this in-site URL to initially be a Sign In with BitBadges URL here with eventual redirect support to your destination URL. Authentication becomes streamlined this way.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Use the BitBadges API

To lookup claim state or recent claim attempts by address or other claim information, use the BitBadges API to query this data. Then, implement your logic as you see fit.

{% content-ref url="bitbadges-api-claims/verifying-claim-attempts-w-the-api.md" %}
[verifying-claim-attempts-w-the-api.md](bitbadges-api-claims/verifying-claim-attempts-w-the-api.md)
{% endcontent-ref %}

