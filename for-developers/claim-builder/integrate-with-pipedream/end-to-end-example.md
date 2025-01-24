# End to End Example

This will walk you through an entire end-to-end auto-completion flow for Pipedream. This will assume you need the whole stack of user authentication. If you do not, you can remove some of these steps and adapt for your use case.

With user authentication, you will need your users to go through the Pipedream Connect authorize flow somehow. Each user will be identified by an `external_user_id` that you set, and once they authorize, you can specify to use that user's authorization details in the automation flow. Their authorization details are stored under that user ID within your Connect app.

See docs here: [https://pipedream.com/docs/connect](https://pipedream.com/docs/connect). When creating a project, you can also get a step by step tutorial through the Connect tab.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

We leave this step open ended up to you. For the sake of the tutorial, we are going to assume that the claim is to be auto-completed upon user authorization. We will also use the Pipedream SDK Connect Link feature to outsource the code, but Pipedream also lets you self-implement for more custom flows.

You will need to create a **token** (short-lived) which can be used to create a Connect Link (see [https://pipedream.com/docs/connect/connect-link](https://pipedream.com/docs/connect/connect-link)). Note the `external_user_id`you use for this user.

```typescript
import { serverConnectTokenCreate } from "./server"
 
const { token, expires_at } = await serverConnectTokenCreate({
  external_user_id: externalUserId  // The end user's ID in your system
});
```

Generate the link like and redirect your users there. This is open-ended. If you want to have a headless no frontend plugin, you can consider adding the Custom Instructions plugin in a claim with a link to a handler endpoint. Because tokens are short-lived and generated dynamically, this should be a proxy one that generates the Connect URL and redirects users there (User -> Proxy Handler -> Generate Connect Link -> Redirect to Connect Link).&#x20;

```
https://pipedream.com/_static/connect.html?token={token}&connectLink=true&app={appSlug}
```

Once the user has completed the authorization, you can now sue that `external_user_id`to perform authenticated requests.&#x20;

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

For this tutorial, we will auto-complete a claim with their no-code workflow ([https://pipedream.com/docs/connect/workflows](https://pipedream.com/docs/connect/workflows)). Follow along here for implementation details. Below, we will explain at a high level and will skip over some lower level details.

1. Create a workflow with a HTTP POST webhook trigger.
2. Configure authorization for the webhook. This can be done in a couple ways.
   1. Use Pipedream OAuth
   2. Check for a secret hardcoded value (add a step after) to make sure you are the origin of the request
   3. No authentication - if you do not add authentication, the only thing saving the endpoint from unwanted requests is the knowledge of the endpoint itself. It is important to not leak it if this is your approach
3. When adding your custom actions, select to use the end user's authentication. See the Pipedream docs for testing this. You will probably need to generate a test account and specify the external user ID in the headers.
   1. Ex: For adding a Slack action with the user's authentication, add another step with Slack and select the little switch icon to use user authentication.
4. We recommend using the API request w/ code (NodeJS) feature. You customize your criteria checks here. You may setup custom parameters parsed from the trigger per claim or static hardcoded props. We leave this open-ended up to you. The only requirement is that if a user that does not meet the criteria, this step should throw an error / fail.

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

5. Lastly, set up the BitBadges action step as the final action. We refer you to the workflow actions for the options here. Typically, you will auto-complete claims if you have the user's crypto address. If not, it may involve setting up and adding a dynamic store. Make sure to test or simulate before actually  claiming for real.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

6. The workflow setup is now complete. The workflow will be triggered upon the HTTP request. It will then use the provided `external_user_id`and execute the claim criteria checks from your configured apps and finally, it will complete a BitBadges claim or add to a dynamic store as the final action properly gating the claim.



You can then invoke the workflow per unique `external_user_id`as shown here with the SDK. Or, you can also trigger via HTTP.&#x20;

```typescript
import { createBackendClient, HTTPAuthType } from "@pipedream/sdk/server";
 
// These secrets should be saved securely and passed to your environment
const pd = createBackendClient({
  environment: "development", // change to production if running for a test production account, or in production
  credentials: {
    clientId: "{oauth_client_id}",
    clientSecret: "{oauth_client_secret}",
  },
  projectId: "{your_project_id}"
});
 
await pd.invokeWorkflowForExternalUser(
  "{your_endpoint_url}", // pass the endpoint ID or full URL here
  "{your_external_user_id}" // The end user's ID in your system
  {
    method: "POST",
    body: {
      message: "Hello World"
    }
  },
  HTTPAuthType.OAuth // Will automatically send the Authorization header with a fresh token
)
```
