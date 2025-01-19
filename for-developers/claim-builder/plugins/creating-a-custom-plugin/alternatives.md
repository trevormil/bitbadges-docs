# Pre-Built Webhook Plugins

### Overview

We provide certain prebuilt webhook plugins in the site to help streamline the process. These don't have all the customization options but allow you to easily set up a webhook by simply just configuring parameters. No need to create a totally custom plugin.

Webhooks can also be sent to tools like Zapier, enabling you to create complex custom automation flows. Or, certain plugins are aliases for receiving specific schemes of attestations like WITNESS proofs.

If you do not want to actually set up a full handler, you can also use the Forms plugin which allows you to store and view the requests in-site.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Note**: These plugins only support a simple POST request and expect a 200 OK response. If you require more custom logic, a custom plugin is necessary.

### Configuration

* **User Inputs**: Hardcoded to have no additional inputs.
* **POST Routes**: Customizable routes for your needs.
* **User Details**: Optionally select to receive user details, like their address or connected socials (only identifying information and no authentication tokens or details)
* **Validation Secret**: Customize a validation secret to confirm requests are from BitBadges.
* **Expected Response:** 200 OK within 10 seconds
  * For success logic, you may consider returning 200 early and asynchronously process.

### Implementation

The request/response flow mirrors that of custom plugins, with the `pluginSecret` replaced by the inputted validation secret. For detailed implementation guidance, refer to the respective documentation.

<figure><img src="../../../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>
