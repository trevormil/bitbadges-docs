# Alternatives - Webhook URLs

### Custom Validation URL / Success Webhook Plugin: Streamlined Alternative

To streamline the plugin process, we also offer prebuilt webhook plugins that allow you to implement logic via your custom URL The Custom Validation URL will check logic during the claim and fail if the logic fails. The Success Webhook plugin will only be sent after successful claims.

This is a streamlined alternative that can be used for many use cases with no setup required (just select it in the directory in-site). Note that no user inputs, state management, etc are available, just a simple POST request with a 200 OK expected. If you need additional features like this, you should create a custom plugin.

**Successes**

We expect a 200 response within a reasonable time (10 seconds) for both. For success webhooks, typically you may be implementing long-running async logic. You should send a 200 OK when the webhook is received and implement your details after.&#x20;

If we do not receive a 200 OK for the successs webhoook plugins, we use an exponential backoff and will resend until we do.

**Configuration**

Both are hardcoded to have no additional user inputs and are POST routes of your choice. You can optionally choose to pass user details like their address or connected socials (if needed).

You can also customize a validation secret to ensure that the request is coming from BitBadges and not another origin.

**Implementation**

This uses the same request / response flow as custom plugins themselves except the pluginSecret is the inputted validation secret instead.  We refer you to those docs to avoid repetition (if needed).

**Using with Zapier / Other Tools**

Webhooks can also be sent to other tools like Zapier which allow you to implement complex custom automation flows.&#x20;

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

