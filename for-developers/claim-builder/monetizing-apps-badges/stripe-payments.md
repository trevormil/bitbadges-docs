# Stripe Payments

Claims can be gated with Stripe payments to implement use cases such as recurring membership payments, concert tickets, etc. There are a couple implementation approaches you can take.&#x20;

IMPORTANT: Note that these approaches show you how to implement the core success flows, but you should also consider when stuff goes wrong (Zapier is down, user refreshes and loses their payment ID, etc).

### Integrate with Zapier

Connect your claim to auto-complete upon receiving successful Stripe payments using Zapier.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### BitBadges API Auto Completion

Upon verifying a successful payment intent with Stripe, you can auto-complete the claim with the BitBadges API. For the setup of Stripe, we refer you to their documentation.&#x20;

{% embed url="https://docs.stripe.com/payments/quickstart?lang=node&client=next" %}

For auto-completing the claims, we refer you here.

{% content-ref url="../auto-completing-claims/auto-complete-claims-w-bitbadges-api.md" %}
[auto-complete-claims-w-bitbadges-api.md](../auto-completing-claims/auto-complete-claims-w-bitbadges-api.md)
{% endcontent-ref %}

For the webhook approach, this may look something like the following:

<pre class="language-typescript"><code class="lang-typescript">export const successWebhook = async (req: Request, res: Response) => {
  try {
    const sig = req.headers['stripe-signature'] ?? '';

    let event;
    try {
      event = stripe.webhooks.constructEvent(req.body, sig, endpointSecret);
    } catch (err) {
      res.status(400).send(`Webhook Error: ${err.message}`);
      return;
    }

    // Handle the event
    switch (event.type) {
      case 'payment_intent.succeeded':
        const paymentIntent = event.data.object;

        //TODO: Auto-completion logic
        const address = mustConvertToCosmosAddress(paymentIntent.metadata.cosmosAddress);
        const res = await BitBadgesApi.completeClaim(claimId, address, { ...body });
<strong>        console.log(res.claimAttemptId);
</strong>      default:
        console.log(`Unhandled event type ${event.type}`);
        return res.status(400).end();
    }

    // Return a 200 res to acknowledge receipt of the event
    return res.send();
  } catch (e) {
    console.error(e);
    return res.status(500).send();
  }
};
</code></pre>

### Custom Plugin

You can also build a custom plugin that directs the user to your payment page and handles claims via your plugin backend handler. For this, we refer you to [https://github.com/BitBadges/bitbadges-plugins](https://github.com/BitBadges/bitbadges-plugins) with the stripe example folders and files for a full E2E quickstarter.

To implement, you will use the claim token approach where the unique claim token is the unique payment ID (thus enforcing one claim per ID).

{% content-ref url="../plugins/creating-a-custom-plugin.md" %}
[creating-a-custom-plugin.md](../plugins/creating-a-custom-plugin.md)
{% endcontent-ref %}
