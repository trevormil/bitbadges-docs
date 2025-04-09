# Checking Custom Criteria

If the in-site plugins are not enough on the criteria side, consider one of the following:

## Dynamic Stores

Dynamic stores can be created in the developer portal. They are simply a list of users stored by BitBadges, managed by you. You attach it to a claim and gate the claim to users in the list.

Stores are nice because they are not tied to a specific claim, and you do not have to deal with addresses if not needed. They are serverless.

You can:

1. Update users manually in-site
2. Update programmatically via the API
3. Update via Zapier - Triggers from 7000+ apps -> add to dynamic store

{% content-ref url="dynamic-stores/" %}
[dynamic-stores](dynamic-stores/)
{% endcontent-ref %}

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Custom Webhooks / Plugins

Configure your claim with custom webhooks. Use the "Check Your Own Criteria" plugin. Alternatively, you can use the Forms / Collect Inputs plugin to instead let us store the requests for you, and you can fetch the details in-site / export to CSV format.

Note: The in-site webhook plugins are streamlined alternatives to building your own custom plugins which are a little more feature-rich and reusable.

Be mindful though that if you are checking criteria, you should have verification BEFORE the claim is completed. Post-success hooks cannot affect the outcome.

{% content-ref url="plugins/" %}
[plugins](plugins/)
{% endcontent-ref %}

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Auto-Completion

You could also initiate the completion of claims on behalf of users with the API or via Zapier. This is advanced and a custom plugin / webhook is oftnetimes a better alternative.
