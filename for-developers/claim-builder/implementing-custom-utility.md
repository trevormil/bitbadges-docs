# Implementing Custom Utility

Need to perform some additional action upon the user claiming successfully?

## **Preconfigured Plugins**

The easiest and most typical approach is to just do this with preconfigured plugins. No code required. Many use cases are already implemented for you and will auto execute for you if configured. For example, the Send BitBadges Notification plugin or Assign Discord Role.

<figure><img src="../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

## **Integrations**

Some integrations may check claims automatically for you. For example, create a WordPress gated site by just entering the claim ID to check in the settings.

{% content-ref url="../authenticating-with-bitbadges/framework-templates/wordpress.md" %}
[wordpress.md](../authenticating-with-bitbadges/framework-templates/wordpress.md)
{% endcontent-ref %}

## **In-Site Rewards - URLs / Content**

When creating rewards on the claim builder page, you can also link gated content / URLs to only be visible to users upon successfully claiming.

You may want additional authentication depending on your tolerance level. You can even consider this in-site URL to initially be a Sign In with BitBadges URL here with eventual redirect support to your destination URL. Authentication becomes streamlined this way.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Use the BitBadges API

To lookup claim state or recent claim attempts by address or other claim information, use the BitBadges API to query this data. Then, implement your logic as you see fit.

{% content-ref url="bitbadges-api-claims/verifying-claim-attempts-w-the-api.md" %}
[verifying-claim-attempts-w-the-api.md](bitbadges-api-claims/verifying-claim-attempts-w-the-api.md)
{% endcontent-ref %}

## Use Post-Success Zaps

If you want to automate this process, consider using Zapier to auto-execute logic upon claim successes per user.

For example, new claim -> add to Mailchimp list or add to Google Sheets.

<figure><img src="../../.gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

{% content-ref url="automate-w-zapier/post-success-zaps.md" %}
[post-success-zaps.md](automate-w-zapier/post-success-zaps.md)
{% endcontent-ref %}

## **Use Post-Success Webhooks / Plugins / Serverless Request Bin**

<figure><img src="../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

Set up a custom success webhook or plugin (post-success) and receive the configured information (plus any custom user inputs or configured user socials you want to receive) via the payload. By the nature of it being a post-success webhook, you do not even need to verify the claim attempt was successful.

Or, the Collect User Inputs (request bin) plugin is a serverless alternative! Instead of needing your own handler, we store the requests for you. You access them in-site or via the API.

<figure><img src="../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>
