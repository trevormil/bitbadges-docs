# API Handler

The outgoing request (from BitBadges to your plugin) will be made up of the custom body inputs (passed from your frontend), the claim parameters, plus some contextual information about the claim and the claiming user.

-   **Plugin Secret:** A plugin secret value that you can use to verify BitBadges as the origin of the call. This is secret only to you and can be obtained via the developer portal when creating your plugin.
-   **Claiming Address:** The **bitbadgesAddress** of the user who is attempting to claim (bb-prefixed Cosmos address).
    -   Note the claiming address may not be verified (signed in) dependeing on the configuration (the user must have the Signed In to BitBadges plugin). If you need to make sure that the user is signed in, use the **isAddressSignedIn** field. This will be true if the claiming user is signed in as the claiming address. All other socials you can assume have been verified / signed in.
-   **Claim Information**: Lastly, we also pass the **claimId,** as well as the claim's **createdAt** and **lastUpdated** timestamps. These can be used, for example, to implement version control systems on your end.
-   **Claim Attempt ID:** The claim attempt ID is the ID of the attempt, and you can use it to track the status of the claim (whether it eventually fails or succeeds).
-   **Attempt Status:** The attempt status (attemptStatus) will be 'executing' during the execution of the claim. If you subscribe to success status webhooks (in the configuration), we will also send a second request (with same body and headers) and \_attemptStatus='success'. This can be used to trigger post-claim logic that needs to wait until completion.
-   **Simulation (Dry Run) Flag:** The **\_isSimulation** flag tells you whether this is a known dry run.

For POST, PUT, and DELETE requests, we pass the values over the body. For GET, we pass them over the GET params. You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is the desired type as well (i.e. GET vs POST vs DELETE vs PUT).

```typescript
const payload = {
    ...customBody, //if applicable
    ...allConfiguredParams, //if applicable

    // Context info

    email: 'bob@abc.com', //If pass email is configured
    discord: { id: '...', username: '...', discriminator: '...' }, //If configured
    twitch: { id: '...', username: '...' }, //If configured
    twitter: { id: '...', username: '...' }, //If configured
    github: { id: '...', username: '...' }, //If configured
    google: { id: '...', username: '...' }, //If configured
    pluginSecret: pluginDoc.pluginSecret,
    claimId: context.claimId,
    claimAttemptId: context.claimAttemptId,
    bitbadgesAddress: context.bitbadgesAddress, //If pass address is configured
    _attemptStatus: context._attemptStatus,
    lastUpdated: context.lastUpdated,
    createdAt: context.createdAt,
    version: context.version,
};
```

### **Identifying the Claiming User**

If you need to identify the claiming user, we pass their address + other requested socials to your endpoint. The socials will all be verified and signed in on our side. The address will be authorized if your plugin specifies the require sign in? option or the claim creator requires sign in.

Although that may not be enough if you identify your users in another way, or you may just not want to fully trust BitBadges. You can also simply check via a secret authorization code. Give them the authorization code on your end while authenticated. Have them enter it as a custom user input in-site. Then, verify it on your end.

### **Responses**

All responses expect a 200 success OK status code within 10 seconds for a successful attempt. For any of the below, do not assume that a 200 OK response means a successful claim and a successful set of the new state. Think of this as a hypothetical state transition IF the claim is eventually successful.

Note: Ensure the returned JSON object keys do not contain any "." characters because that may mess up the state handler. For example, emails should be bob@abc\[dot]com rather than bob@abc.com.

**Stateless Preset**

The stateless preset is simple. If we receive the 200, the plugin is successful. Nothing else is checked via the response. Everything is handled on your end (if you have state).

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)   (1).png" alt=""><figcaption></figcaption></figure>

**Claim Token Preset**

This preset expects a { claimToken} in the response. The claim token is a one-time use only claim code. Issuing claim tokens is left up to you.

<figure><img src="../../../../../.gitbook/assets/image (141) (1).png" alt=""><figcaption></figcaption></figure>

**Claim Numbers Preset**

This preset expects a { claimNumber } in the response. The claim number is the claim number that will be assigned if the claim number is successful. Claim numbers are 0-based, so claimNumber === 0 is the first claim, and so on.

IMPORTANT: Only one plugin can control claim number assignment. If you select this approach, claims that use this plugin will not be compatible with any other plugin that uses the claim number preset.

### **Success Hook Responses**

For success hooks (\_attemptStatus=success), we also expect a 200 OK within 10 seconds. If you need to do asynchronous processing, return 200 OK early and process as you desire.

Otherwise, for failed attempts, we will retry later with an exponential backoff policy.

### **Error Responses**

If your plugin fails, we will save the error for debugging / monitor purposes. It may be displayed to the claiming user and / or claim cretor, so make errors informative but do not reveal sensitive information.

Please follow the { message } interface for returned JSON error responses.

### Example Plugin Handler

<pre class="language-typescript"><code class="lang-typescript"><strong>//TODO: Fill in missing information
</strong>const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  try {
    //Step 1: Handle the request payload from the plugin
    const body = req.body; //We assume the plugin sends the payload in the body of the request (change this for GET)
    const { claimId, pluginSecret, bitbadgesAddress, lastUpdated, createdAt } = body;
    const { ...otherCustomProvidedInputs } = body;
    
    //Handle anything specific to dry runs _isSimulation

    //Step 2: Verify BitBadges as origin by checking plugin secret is correct
    const YOUR_PLUGIN_SECRET = '';
    if (pluginSecret !== YOUR_PLUGIN_SECRET) {
      return res.status(401).json({ message: 'Invalid plugin secret' });
    }

    //Step 3: Implement your custom logic here. Consider checking the plugin's creation / last updated time to implement version control.
    //TODO: 

    //Step 4: Return the response to the plugin based on your configured state function preset
    // const claimTokenRes = { claimToken: '...'  }
    // const statelessRes = {};
    return res.status(200).json({});
  } catch (err) {
    console.log(err);
    return res.status(401).json({ message: `${err}` });
  }
};

export default handlePlugin;
</code></pre>
