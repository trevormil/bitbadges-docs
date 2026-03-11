# Checking Custom Criteria

If the in-site plugins are not enough on the criteria side, consider one of the following:

## Dynamic Stores

Dynamic stores can be created in the developer portal. They are simply a list of users stored by BitBadges, managed by you. You attach it to a claim and gate the claim to users in the list.

Stores are nice because they are not tied to a specific claim, and you do not have to deal with addresses if not needed. They are serverless.

You can:

1. Update users manually in-site
2. Update programmatically via the API


{% content-ref url="dynamic-stores/" %}
[dynamic-stores](dynamic-stores/)
{% endcontent-ref %}

<figure><img src="../../.gitbook/assets/image (26) (1).png" alt=""><figcaption></figcaption></figure>

## Custom Webhooks / Plugins

Create custom plugins in the [developer portal](https://bitbadges.io/developer) to check your own criteria. Your plugin receives the user's address and any custom inputs, and returns success/failure.

Be mindful though that if you are checking criteria, you should have verification BEFORE the claim is completed. Post-success hooks cannot affect the outcome.

{% content-ref url="plugins/" %}
[plugins](plugins/)
{% endcontent-ref %}

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

## Auto-Completion

You could also initiate the completion of claims on behalf of users with the API. This is advanced and a custom plugin is oftentimes a better alternative.
