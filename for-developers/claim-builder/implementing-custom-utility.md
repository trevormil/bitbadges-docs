# Implementing Custom Utility

Need to perform some additional action upon the user claiming successfully? There are a few ways you can implement this. Note that depending on your use case, you may also need to authenticate the user as well on your end. We recommend using Sign In with BitBadges for this.

{% content-ref url="../authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../authenticating-with-bitbadges/)
{% endcontent-ref %}

## **Preconfigured Plugins**

The easiest and most typical approach is to just do this with preconfigured plugins. Many use cases are already implemented for you and will auto execute for you if configured. For example, the Send BitBadges Notification plugin or Assign Discord Role.

<figure><img src="../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

## **In-Site Rewards**

When creating rewards on the claim builder page, you can also link gated content / URLs to only be visible to users upon successfully claiming. You can add dynamic variables, but note that this does not protect against the users sharing the secret URL.&#x20;

You may need additional authentication depending on your tolerance level. You can even consider this in-site URL to initially be a Sign In with BitBadges URL here with eventual redirect support to your destination URL. Authentication becomes streamlined this way.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Use the BitBadges API

To lookup claim state or recent claim attempts by address or other claim information, use the BitBadges API to query this data. Then, implement your logic as you see fit.

```typescript
await BitBadgesApi.getClaimAttempts(...)
```

{% content-ref url="bitbadges-api-claims/" %}
[bitbadges-api-claims](bitbadges-api-claims/)
{% endcontent-ref %}

## Use Post-Success Zaps

If you want to automate this process, consider using Zapier to auto-execute logic upon claim successes.&#x20;

<figure><img src="../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

{% content-ref url="automate-w-zapier/post-success-zaps.md" %}
[post-success-zaps.md](automate-w-zapier/post-success-zaps.md)
{% endcontent-ref %}

## **Custom Plugins / Webhooks**

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

Set up a custom validation URL (during execution) or success webhook (post-success) and receive the following information (plus any custom user inputs or configured user socials you want to receive) via the payload. See the custom plugin documentation for more information. Note the passed socials are just the user identifiers and have no sensitive authentication info like access tokens.

This can either be setup through the in-site plugins as shown above, or you can custom create and potentially publish your own from scratch.

<figure><img src="../../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

```json
{
  "pluginSecret": "asdasd",
  "claimId": "24c7c6afa961c7b519d90a43ddef6572",
  "claimAttemptId": "abc123",
  "lastUpdated": 1800000000000,
  "createdAt": 1800000000000,
  "version": "0",
  "_isSimulation": false,
  "_attemptStatus": "success",
  "bitbadgesAddress": "bb1...",
  "ethAddress": "0x...",
  "solAddress": "...",
  "btcAddress": "bc1...",
  "email": {
    "id": "bob@abc.com",
    "username": "bob@abc.com"
  }
}
```
