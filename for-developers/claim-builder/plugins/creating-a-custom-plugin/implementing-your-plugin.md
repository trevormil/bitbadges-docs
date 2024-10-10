# Implementing Your Plugin

To create, publish, and maintain your plugin, go to [https://bitbadges.io/developer](https://bitbadges.io/developer) and use the Plugins tab. This should walk you through the whole process of configuration and submitting it to be used on the BitBadges site. If you are confused at any point, refer back to this documentation.

On your end, you will need to setup a HTTP handler API to accept the incoming requests -> perform your validation logic -> return with valid response.

### End to End Quickstarter

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-plugins" %}

### Handling User / Creator Inputs

If your plugin requires inputs from the claiming user or claim creator, you can do this in the BitBadges site or via a window.postMessage from a custom frontend / tool. All inputs will be passed along to your handler. If none are provided, we assume there is nothing needed to be passed.

Note to be compatible with Zapier (and possibly API auto-claiming), user inputs are typically not allowed (because the user is not manually initiating anything).

User inputs are private, but creator inputs have the option to be public or private to the user.

**Option 1: In-Site**

When creating the plugin, configure the expected schemas. We will prompt the users / creators to enter such information in-site via a form. All outsourced to BitBadges.

<figure><img src="../../../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

**Option 2: Build a Custom Inputs Frontend**

Consider creating your own frontend that the user will be redirected to. You will pass the parameters back to BitBadges through browser communication. Get creative and combine approaches. For example, handle secure stuff on your end -> grant an authorization / claim code -> have user add it directly in the site.

For user inputs, see here:

{% content-ref url="custom-input-frontends-advanced.md" %}
[custom-input-frontends-advanced.md](custom-input-frontends-advanced.md)
{% endcontent-ref %}

For creator inputs, see configuration tools which automatically for the claim creator (e.g. auto-populate emails from a specific service). See the bitbadges/bitbadges-tools repository on Github.

{% content-ref url="../../configuration-tools.md" %}
[configuration-tools.md](../../configuration-tools.md)
{% endcontent-ref %}

**Supported Schema Types**

Primitives: String, Number, Boolean

Date: UNIX millisecond timestamp

URL: Stringified URL

Attestations (for user inputs): [User inputted attestation proofs](../../../core-concepts/verifiable-attestations/) array.

### Identifying the Claiming User

You can also select to automatically pass supported identifying details about the user (e.g. crypto addresses, Discord, X, GitHub, etc). We will authenticate the user on our end where needed, and you can use their identifying information to execute queries (e.g. public GitHub contributions). Note no access tokens or auth details are passed along so private, authorized requests are not possible with this information.

<figure><img src="../../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

### **Backend Handler**

The outgoing request (from BitBadges to your plugin) will be made up of the custom body inputs (passed from your frontend), the claim parameters, plus some contextual information about the claim and the claiming user.

* **Plugin Secret:** A plugin secret value that you can use to verify BitBadges as the origin of the call. This is secret only to you and can be obtained via the developer portal when creating your plugin.
* **Claiming Address:** The **cosmosAddress** of the user who is attempting to claim.
* **Simulation**: For simulated claims, we pass the \_isSimulation = true. You can use this flag, for example, to not update important state information for simulations.
* **Claim Information**: Lastly, we also pass the **claimId,** as well as the claim's **createdAt** and **lastUpdated** timestamps. These can be used, for example, to implement version control systems on your end.
* **Claim Attempt Id:** The claim attempt ID is the ID of the attempt, and you can use it to track the status of the claim (whether it eventually fails or succeeds).
* **Prior State:** If you select the state transition preset type (see response section), we will pass the current state via **priorState**.
* **Curr / Max Uses:** We also provide you with the current number of successful uses (not counting the current claim) and total max number of uses.
* **Attempt Status:** The attempt status (attemptStatus) will be 'executing' during the execution of the claim. If you subscribe to success status webhooks (in the configuration), we will also send a second request (with same body and headers) and \_attemptStatus='success'. This can be used to trigger post-claim logic that needs to wait until completion.

For POST, PUT, and DELETE requests, we pass the values over the body. For GET, we pass them over the GET params. You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is the desired type as well (i.e. GET vs POST vs DELETE vs PUT).

```typescript
const payload = {
    ...customBody, //if applicable
    ...allConfiguredParams, //if applicable

    // Context info

    email: { id: 'bob@abc.com' }, //If pass email is configured
    discord: { id: '...', username: '...', discriminator: '...' }, //If configured
    twitch: { id: '...', username: '...' }, //If configured
    twitter: { id: '...', username: '...' }, //If configured
    github: { id: '...', username: '...' }, //If configured
    google: { id: '...', username: '...' }, //If configured
    priorState: {}, //If using state transition preset function (see below)
    pluginSecret: pluginDoc.pluginSecret,
    claimId: context.claimId,
    claimAttemptId: context.claimAttemptId,
    cosmosAddress: context.cosmosAddress, //If pass address is configured
    ethAddress: context.ethAddress, //If pass address is configured
    solAddress: context.solAddress, //If pass address is configured
    btcAddress: context.btcAddress, //If pass address is configured
    _isSimulation: context._isSimulation,
    _attemptStatus: context._attemptStatus,
    lastUpdated: context.lastUpdated,
    createdAt: context.createdAt,
    maxUses: context.maxUses,
    currUses: context.currUses,
    version: context.version,
};
```

### **Responses**

All responses expect a 200 success OK status code for a successful attempt. For any of the below, do not assume that a 200 OK response means a successful claim and a successful set of the new state. Think of this as a hypothetical state transition IF the claim is eventually successful.

Note: Ensure the returned JSON object keys do not contain any "." characters because that may mess up the state handler. For example, emails should be bob@abc\[dot]com rather than bob@abc.com.

**Stateless Preset**

The stateless preset is simple. If we receive the 200, the plugin is successful. Nothing else is checked via the response. Everything is handled on your end (if you have state).

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Claim Token Preset**

This preset expects a { claimToken} in the response. The claim token is a one-time use only claim code. Issuing claim tokens is left up to you.

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**State Transitions Preset**

This preset expects a { newState } in the response. If the claim is successful, we will set the current state stored in our plugin to the new state. If this option is selected, you also have access to the prior state in the request payload.

**Claim Numbers Preset**

This preset expects a { claimNumber } in the response. The claim number is the claim number that will be assigned if the claim number is successful. Claim numbers are 0-based, so claimNumber === 0 is the first claim, and so on.

The prior state of the number of uses plugin will be passed via the request payload.

IMPORTANT: Only one plugin can control claim number assignment. If you select this approach, claims that use this plugin will not be compatible with any other plugin that uses the claim number preset.

Another important decision you will have to consider is whether to reuse your plugin for address lists because claim numbers do not matter for address list plugins. So basically, your plugin will function like the stateless preset for address list claims, if selected.

### **Error Responses**

If your plugin fails, we will save the error for debugging / monitor purposes. It may be displayed to the claiming user and / or claim cretor, so make errors informative but do not reveal sensitive information.

Please follow the { message } interface for returned JSON error responses.

### Example Plugin Handler

<pre class="language-typescript"><code class="lang-typescript"><strong>//TODO: Fill in missing information
</strong>const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  try {
    //Step 1: Handle the request payload from the plugin
    const body = req.body; //We assume the plugin sends the payload in the body of the request (change this for GET)
    const { priorState, claimId, pluginSecret, cosmosAddress, ethAddress, solAddress, btcAddress, _isSimulation, lastUpdated, createdAt } = body;
    const { ...otherCustomProvidedInputs } = body;

    //Step 2: Verify BitBadges as origin by checking plugin secret is correct
    const YOUR_PLUGIN_SECRET = '';
    if (pluginSecret !== YOUR_PLUGIN_SECRET) {
      return res.status(401).json({ message: 'Invalid plugin secret' });
    }

    //Step 3: Implement your custom logic here. Consider checking the plugin's creation / last updated time to implement version control.
    //TODO: 

    //Step 4: Return the response to the plugin based on your configured state function preset
    // const claimTokenRes = { claimToken: '...'  }
    // const stateTransitionRes = { ...newState }
    // const statelessRes = {};
    return res.status(200).json({});
  } catch (err) {
    console.log(err);
    return res.status(401).json({ message: `${err}` });
  }
};

export default handlePlugin;
</code></pre>
