# Implementing Post-Success Utility

Need to perform some additional action upon the user claiming successfully? There are a few ways you can implement this. Note that depending on your use case, you may also need to authenticate the user as well on your end. We recommend using Sign In with BitBadges for this.

{% content-ref url="../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../authenticating-with-bitbadges/)
{% endcontent-ref %}

## **Preconfigured Plugins**

The easiest and most typical approach is to just do this with preconfigured plugins. No code required. Many use cases are already implemented for you and will auto execute for you if configured. For example, the Send BitBadges Notification plugin or Assign Discord Role.

<figure><img src="../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

## Integrations

Some integrations may check claims automatically for you. For example, create a WordPress gated site by just entering the claim ID to check in the settings.

{% content-ref url="../authenticating-with-bitbadges/framework-templates/wordpress.md" %}
[wordpress.md](../authenticating-with-bitbadges/framework-templates/wordpress.md)
{% endcontent-ref %}

## **In-Site Rewards - URLs / Content**

When creating rewards on the claim builder page, you can also link gated content / URLs to only be visible to users upon successfully claiming.&#x20;

You may want additional authentication depending on your tolerance level. You can even consider this in-site URL to initially be a Sign In with BitBadges URL here with eventual redirect support to your destination URL. Authentication becomes streamlined this way.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Use the BitBadges API

To lookup claim state or recent claim attempts by address or other claim information, use the BitBadges API to query this data. Then, implement your logic as you see fit.

{% content-ref url="verifying-claims-w-the-api.md" %}
[verifying-claims-w-the-api.md](verifying-claims-w-the-api.md)
{% endcontent-ref %}

## Use Post-Success Zaps

If you want to automate this process, consider using Zapier to auto-execute logic upon claim successes per user.

<figure><img src="../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

{% content-ref url="automate-w-zapier/post-success-zaps.md" %}
[post-success-zaps.md](automate-w-zapier/post-success-zaps.md)
{% endcontent-ref %}

## **Custom Plugins / Webhooks**

<figure><img src="../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

Set up a custom validation URL (during execution) or success webhook (post-success) and receive the following information (plus any custom user inputs or configured user socials you want to receive) via the payload. See the custom plugin documentation for more information. Note the passed socials are just the user identifiers and have no sensitive authentication info like access tokens.

This can either be setup through the in-site plugins as shown above, or you can custom create and potentially publish your own from scratch.

<figure><img src="../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>
