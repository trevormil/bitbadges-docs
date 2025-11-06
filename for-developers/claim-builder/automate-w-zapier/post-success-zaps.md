# Post-Success Zaps

Post-success Zaps are a great way to implement custom utility from over 7000+ apps upon claim successes.

There are two options:

1. Use Webhooks by Zapier plugin and catch a POST request + Post-Success Zap plugin in your claim. This is recommended as it is much more feature complete and allows you to get social identifiers like emails, apps, etc.
2. Use the Claim Success trigger provided by BitBadges x Zapier Integration



For this tutorial, we will showcase Option 1 (recommended):

1. Setup your Zap (in the Zapier interface) with Webhooks by Zapier and catch a POST request

<figure><img src="../../../.gitbook/assets/image (377).png" alt=""><figcaption></figcaption></figure>

2. Configure your Post-Success Zapier Webhook plugin in the BitBadges site. Add the webhook URL it gives you from Step 1. For Zapier, the validation secret is not as important, but you can additionally add a step to check it within the Zap. The JSON preview it shows you will give you all the available fields you can use.

<figure><img src="../../../.gitbook/assets/image (380).png" alt=""><figcaption></figcaption></figure>

3. Use the Send Test Request in the BitBadges site to complete the test / simulation step in your Zap.

<figure><img src="../../../.gitbook/assets/image (378).png" alt=""><figcaption></figcaption></figure>

4. Configure your action with any app and dynamically replace values where needed. For example, if your action is an outbound send email, you will need to parse the email from the webhook and automatically populate the recipient address for the email.

<figure><img src="../../../.gitbook/assets/image (379).png" alt=""><figcaption></figcaption></figure>

5. Setup the rest of your Zap, create the claim, and you are good to go!
