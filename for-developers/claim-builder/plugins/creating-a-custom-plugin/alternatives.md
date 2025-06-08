# Pre-Built Webhook Plugins

Configure prebuilt webhook plugins in the claim builder without needing to create an entirely new custom plugin. Just enter the endpoint and configure what you want to receive.If you do not want to actually set up a full handler, you can also use the Forms plugin which allows you to store and view the requests in-site (serverless).

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Note**: These plugins only support a simple POST request and expect a 200 OK response. If you require more custom logic or reusability, a custom plugin is necessary.

### Configuration

* **POST Routes**: Customizable routes for your needs.
* **User Details**: Optionally select to receive user details, like their address, custom inputs, or connected socials (only identifying information and no authentication tokens or details)
* **Validation Secret**: Customize a validation secret to confirm requests are from BitBadges. This is what is entered in the form.
* **Expected Response:** 200 OK within 10 seconds
  * For non-critical logic, consider returning 200 early and asynchronously process.
  * Stateless

### Forms Plugin  - Serverless

The forms plugin is a serverless alternative. This is titled "Collect User Inputs" in the site.

Think of this like a request storage bin. We store the requests that would've been sent to the webhooks for you. You can then view them in-site or fetch them from the API when needed.

```typescript
// Pre: Get the attempt ID. If you do not have it already, see the API reference endpoints

// GET /api/v0/requestBin/attemptData/{claimId}/{claimAttemptId}
const res = await BitBadgesApi.getAttemptDataFromRequestBin("claim123", "attempt123", { ... });
console.log(res);
// { bitbadgesAddress, email, claimAttemptId } 
```

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Implementation - Handlers

The request/response flow mirrors that of custom plugins, with the `pluginSecret` replaced by the inputted validation secret. For detailed implementation guidance, refer to the respective documentation. The configuration is done in-site via the claim builder.

1. Setup your plugin handler (see plugin documentation for more information)
2. Create your claim and configure the webhook as one of the plugins, specifying your endpoint
3. Test it out to make sure it is working
