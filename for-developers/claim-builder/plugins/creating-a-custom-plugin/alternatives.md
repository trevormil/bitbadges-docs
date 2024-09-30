# Webhooks (Alternative)

### Overview

We provide prebuilt webhook plugins to simplify custom logic implementation:

* **Success Webhook Plugin**: For post-claim logic.
* **Custom Validate URL Plugin**: For checking criteria during execution.

**Note**: These plugins only support a simple POST request and expect a 200 OK response. If you require user inputs or state management, a custom plugin is necessary.

### Passing Socials

You can choose to receive the user’s connected social accounts (e.g., email, Discord) in the payload. If enabled, we will provide the username/ID, but no access tokens or authenticated details will be included.

### Success Criteria

Both plugins expect a 200 response within 10 seconds. For success webhooks, you may implement long-running asynchronous logic. Ensure you send a 200 OK upon receipt, and process further actions afterwards. If a 200 OK is not received, we will use exponential backoff to retry until successful.

### Configuration

* **User Inputs**: Hardcoded to have no additional inputs.
* **POST Routes**: Customizable routes for your needs.
* **User Details**: Optionally pass user details, like their address or connected socials.
* **Validation Secret**: Customize a validation secret to confirm requests are from BitBadges.

### Implementation

The request/response flow mirrors that of custom plugins, with the `pluginSecret` replaced by the inputted validation secret. For detailed implementation guidance, refer to the respective documentation.

### Integrations

Webhooks can also be sent to tools like Zapier, enabling you to create complex custom automation flows.&#x20;
