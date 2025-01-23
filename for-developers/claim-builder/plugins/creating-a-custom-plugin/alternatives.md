# Pre-Built Webhook Plugins

We provide certain prebuilt webhook plugins in the site to help streamline the process. These allow you to easily set up a webhook by simply just configuring the endpoint and what you want to receive. No need to create a totally custom plugin. This is typically the better choice unless you are looking to publish a reusable plugin.

Webhooks can also be sent to tools like Zapier, enabling you to create complex custom automation flows. Or, certain plugins are aliases for receiving specific attestations like WITNESS proofs.&#x20;

If you do not want to actually set up a full handler, you can also use the Forms plugin which allows you to store and view the requests in-site.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Note**: These plugins only support a simple POST request and expect a 200 OK response. If you require more custom logic or reusability, a custom plugin is necessary.

### Configuration

* **POST Routes**: Customizable routes for your needs.
* **User Details**: Optionally select to receive user details, like their address, custom inputs, or connected socials (only identifying information and no authentication tokens or details)
* **Validation Secret**: Customize a validation secret to confirm requests are from BitBadges.
* **Expected Response:** 200 OK within 10 seconds
  * For non-critical logic, consider returning 200 early and asynchronously process.

### Forms Plugin  - Serverless

The forms plugin is a serverless alternative. Think of this like a request storage bin. We store the requests that would've been sent to the endpoints in the claim state. You can then view them in-site or fetch them from the API when needed.

### Implementation

The request/response flow mirrors that of custom plugins, with the `pluginSecret` replaced by the inputted validation secret. For detailed implementation guidance, refer to the respective documentation. The configuration is done in-site.

1. Setup your plugin handler (see plugin documentation for more information)
2. Create your claim and configure the webhook as one of the plugins, specifying your endpoint
3. Test it out to make sure it is working

<pre class="language-typescript"><code class="lang-typescript"><strong>//TODO: Fill in missing information
</strong>const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  try {
    //Step 1: Handle the request payload from the plugin
    const body = req.body; //We assume the plugin sends the payload in the body of the request (change this for GET)
    const { claimId, pluginSecret, bitbadgesAddress, ethAddress, solAddress, btcAddress, lastUpdated, createdAt } = body;
    const { ...otherCustomProvidedInputs } = body;

    //Step 2: Verify BitBadges as origin by checking plugin secret is correct
    const YOUR_PLUGIN_SECRET = '';
    if (pluginSecret !== YOUR_PLUGIN_SECRET) {
      return res.status(401).json({ message: 'Invalid plugin secret' });
    }

    //Step 3: Implement your custom logic here. Consider checking the plugin's creation / last updated time to implement version control.
    //TODO: 

    //Step 4: Return the response to the plugin based on your configured state function preset
    return res.status(200).json({});
  } catch (err) {
    console.log(err);
    return res.status(401).json({ message: `${err}` });
  }
};

export default handlePlugin
</code></pre>

<figure><img src="../../../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
