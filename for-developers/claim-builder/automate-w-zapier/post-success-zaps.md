# Post-Success Zaps

If you want to implement post-success Zaps, consider using the New Claim Success trigger. This will poll and execute the Zap for every new claim that is completed. You have access to the claiming address / claim number (zero-based) in the response.

<figure><img src="../../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

If you need more information like user socials or custom inputs, consider creating a custom plugin or using a webhook plugin to handle this and trigger the Zap manually via the Webhooks by Zapier plugin. Note this should NOT use the BitBadges integration as shown above. It uses the dedicated Webhooks by Zapier trigger.

1. Setup your Zap with Webhooks by Zapier and catch a POST request

<figure><img src="../../../.gitbook/assets/image (204).png" alt=""><figcaption></figcaption></figure>

2. Configure your Zapier Webhook plugin in the BitBadges site. Add the webhook URL it gives you. For Zapier, the validation secret is not as important, but you can additionally add a step to check it within the Zap. The JSON preview it shows you will give you all the available fields you can use.&#x20;

<figure><img src="../../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

3. Use the Send Test Request in the BitBadges site to complete the test / simulation step in your Zap.

<figure><img src="../../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

4. Configure your action and dynamically replace values where needed. For example, if your action is an outbound send email, you will need to parse the email from the webhook and automatically populate the recipient address for the email.

<figure><img src="../../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>

5. Setup the rest of your Zap, create the claim, and you are good to go!
