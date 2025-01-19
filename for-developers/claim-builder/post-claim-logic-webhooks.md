# Implementing Post-Claim Actions (Rewards)

Need to perform some additional action upon the user claiming successfully? There are a few ways you can implement this.

## **Preconfigured Plugins**

The easiest and most typical approach is to just do this with preconfigured plugins. Many use cases are already implemented for you. For example, Send BitBadges Notification plugin.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **In-Site Rewards**

When creating rewards on the claim builder page, you can also link gated content / codes to only be visible to users upon successfully claiming.

Gated content / URLS - We check 1+ successful claim

Codes - We assign a unique code to each successful claim number. See [universal-approach-claim-codes.md](universal-approach-claim-codes.md "mention") for more information (reuses same code).

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Polling Claim State

You can also simply poll the claimed users by the claim ID since the claimees and claim numbers are public. Note this will only give you the claim address and number and not any of the features as custom plugins below. This can be done via the API or as a custom trigger through our Zapier integration.

With this flow, you may find you need to authenticate users via Sign In with BItBadges on your end too. Note your claim may also have assigned, for example, unique badge IDs to each claimee, so you may also be able to authenticate or gate rewards by checking such requirements instead of using solely their address.

{% content-ref url="automate-w-zapier/post-success-zaps.md" %}
[post-success-zaps.md](automate-w-zapier/post-success-zaps.md)
{% endcontent-ref %}

{% content-ref url="fetching-claims-w-api.md" %}
[fetching-claims-w-api.md](fetching-claims-w-api.md)
{% endcontent-ref %}

## **Custom Plugins / Webhooks**

Plugins and the pre-configured webhook plugins can be customized to send requests to custom URL and expect a 200 OK response. There are two types of requests:

* Success Hooks: If configured, we can send a success webhook which will only send upon a successful claim. This can be checked using the \_attemptStatus. If we do not receive a 200 OK, we will exponentially backoff but keep retrying until we do.
  * Typically, you may implement a queue-like system (send 200 OK to denote received -> implemnet your logic via the queue system)
* During Execution Hook: Or, if your logic is critical to whether the claim is successful or not. We can call the URL during the claim execution and fail if we do not receive a 200 OK.

**Webhooks by Zapier**

Consider using the Webhooks by Zapier plugin on Zapier and trigger additional actions upon execution or success. Or, use the Get Claim Success trigger natively supported with the BitBadges integration.

This is super easy as you can connect to 7000+ apps upon a successful claim.

{% content-ref url="automate-w-zapier/post-success-zaps.md" %}
[post-success-zaps.md](automate-w-zapier/post-success-zaps.md)
{% endcontent-ref %}

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

**Custom Webhooks**

Consider using the Custom Validation URL in-site plugin or the Success Webhook in-site plugin and trigger an action from those. The Success Webhook plugin will send a request upon success, but the Custom Validation URL will send during execution (so does not guarantee success). The Custom Validation URL expects a 200 OK, or else, the claim will fail.

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

**Custom Plugins**

Or, you can implement your own custom plugin. In the configuration form, you can select to receive a success webhook with \_attemptStatus: 'success' if you are implementing logic that is dependent on the success of the claim. You can also identify the user in many different ways via the passed socials / their address.

It is super easy to implement (just an HTTP request). See the creating plugins documentation for more information.

```typescript
// At your plugin handler URL

const body = req.body;
/*
{
  bitbadgesAddress: 'bb1zd5dsage58jfrgmsu377pk6w0q5zhc672wamvw',
  claimId: '7a5b61ce3f2cf5eeb89f453c87f2b970328356395bf4ac4a23702d2fb0fb63c9',
  lastUpdated: 1722088273192,
  createdAt: 1722088273192,
  claimAttemptId: '',
  isClaimNumberAssigner: false,
  _attemptStatus: 'executing', // or 'success'
  _isSimulation: false, //True if this is a simulation (dry run) vs not
  instanceId: 'e44ba88643381cd5fa09be288490a92c64add8bcd2327d29a11a4227fab55e5e',
  pluginId: '...',
  
  discord: { username: '', id: '' } //if configured
  //and so on
}
*/

const claimAtemptId = body.claimAttemptId;
const status = await BitBadgesApi.getClaimAttemptStatus(claimAttemptId);
```

## Auto-Completion

And finally, we want to note that if you already are auto-claiming with the BitBadges API, you already know when users claim, so you can just implement whatever you need right there.
