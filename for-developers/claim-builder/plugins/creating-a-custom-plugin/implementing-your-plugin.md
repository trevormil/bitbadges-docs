# Implementing Your Plugin

Get started by going to the developer portal and creating a plugin via the form. This should walk you through the whole process of configuration and submitting it to be used on the BitBadges site.

On your end, you will simply need to setup a HTTP handler API to accept the incoming requests -> perform your validation logic -> return with valid state.

### End to End Quickstarter

{% @github-files/github-code-block url="https://github.com/BitBadges/bitbadges-plugins" %}

### Custom User Inputs

If your plugin requires no user prompting, then you can skip this section.

BitBadges is just the UI / middleman in this flow. While everything is handled via secure communication channels and protocols, you may consider adding an additional layer of abstraction to avoid letting BitBadges know anything secret. For example, keep information secret on your end and use claim codes instead of sending it directly through BitBadges.

Note to be compatible with Zapier (and possibly API auto-claiming), user inputs are typically not allowed (because the user is not manually initiating anything).

**Schemas**

When creating the plugin, configure the expected schemas directly in the form. Users will be prompted to enter such information which will be relayed along to your handler.&#x20;

<figure><img src="../../../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

**Build a Custom Inputs Frontend**

If the inputs are advanced and you would like to automate this process with a more user friendly frontend, consider creating a helper tool that the user will be redirected to. You will pass the parameters back to BitBadges through browser communication.

{% content-ref url="custom-input-frontends-advanced.md" %}
[custom-input-frontends-advanced.md](custom-input-frontends-advanced.md)
{% endcontent-ref %}

You can also get creative and combine approaches. For example, handle secure stuff on your end -> grant an authorization / claim code -> have user add it directly in the site.

**User Authentication + Authorized Requests**

Note that many plugins may require API requests that must be authorized by a specific user first or may require details about the claiming user.&#x20;

For these plugins, you have a couple options.

1. If the method is supported on the BitBadges site (e.g. crypto addresses, Discord, X, GitHub, etc), we give you the option to pass the user's username / ID or other public identifying details (i.e. addresses) to your plugin. We will authenticate the user on our end, and you can use their identifying information to execute queries (e.g. public GitHub contributions). Note no access tokens or auth details are passed along so private, authorized requests are not possible. Typically, this is used for public queries only.
2. The next option is you will need to handle all authentication / authorization that is needed on your end. You can then issue a claim code, unique authorization code, or pass along whatever is needed via the user inputs in the claim body which is to be used in your backend plugin hadnler.
   1. For example, upon claiming, user gets redirected to your service (frontend) -> handle authorization logic -> set claim body -> user claims -> your plugin (backend) is called -> use the claim body in your criteria logic.&#x20;
3. We are also willing to cooperate with you and add your plugin natively to the BitBadges backend. If this is of interest, let us know.

### Custom Creation Parameters

If you need to allow the claim creator to configure parameters (e.g. max 10 uses per user), this can be done in-site via the creation inputs schema section. If none are provided, we assume there is nothing additional the claim creator has to configure.

<figure><img src="../../../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

**Snapshots vs Dynamic Requests**

There are a couple common implementation patterns for plugins. If possible, we recommend the first option.

1. Snapshot + User Auth - At creation time, configure the parameters (potentially with an authorized request from the creator) and no future requests on behalf of the creator need to be made. At claim time, you could use user authentication to check criteria. For example,
   1. Configure all approved emails -> verify user email at claim time
   2. Configure the Twitch channel name -> use user Twitch authorization to check subscription
2. Dynamic - Some plugins, however, may need to execute creator authorized requests upon each claim. For this, you will need to cache the creator details or somehow make it so that you will have authorization from the creator during all claim times.&#x20;
   1. Note that you should consider all possibilities like access token expirations -> refresh functionality.

**Tutorials**

Consider creating a tutorial to walk creators through this process.

{% content-ref url="../../other-tutorials/create-a-tutorial.md" %}
[create-a-tutorial.md](../../other-tutorials/create-a-tutorial.md)
{% endcontent-ref %}

**Helper Tools (Configuration Tools)**

You may also create a helper tool to handle inputs automatically for the claim creator (e.g. auto-populate emails from a specific service). See the bitbadges/bitbadges-tools repository on Github.

{% content-ref url="../configuration-tools.md" %}
[configuration-tools.md](../configuration-tools.md)
{% endcontent-ref %}

### **Backend Request**

The outgoing request (from BitBadges to your plugin) will be made up of the custom body inputs (passed from your frontend), plus some contextual information about the claim and the claiming user.

* **Plugin Secret:** A plugin secret value that you can use to verify BitBadges as the origin of the call. This is secret only to you and can be obtained via the developer portal when creating your plugin.
* **Claiming Address:** The **cosmosAddress** of the user who is attempting to claim.
* **Simulation**: For simulated claims, we pass the \_isSimulation = true. You can use this flag, for example, to not update important state information for simulations.
* **Claim Information**: Lastly, we also pass the **claimId,** as well as the claim's **createdAt** and **lastUpdated** timestamps. These can be used, for example, to implement version control systems on your end.
* **Claim Attempt Id:** The claim attempt ID is the ID of the attempt, and you can use it to track the status of the claim (whether it eventually fails or succeeds).
* **Prior State:** If you select the state transition preset type (see response section), we will pass the current state via **priorState**.&#x20;
* **Curr / Max Uses:** We also provide you with the current number of successful uses (not counting the current claim) and total max number of uses.

For POST, PUT, and DELETE requests, we pass the values over the body. For GET, we pass them over the GET params. You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is the desired type as well (i.e. GET vs POST vs DELETE vs PUT).

<figure><img src="../../../../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

```typescript
const payload = {
    ...customBody,//if applicable
        
    // Context info
    
    email: { id: 'bob@abc.com' }, //If pass email is configured
    discord: { id: '...', username: '...', discriminator: '...' }, //If configured
    twitch: { id: '...', username: '...' }, //If configured
    twitter: { id: '...', username: '...' }, //If configured
    github: { id: '...', username: '...' }, //If configured
    google: { id: '...', username: '...' }, //If configured
    priorState: { }, //If using state transition preset function (see below)
    pluginSecret: pluginDoc.pluginSecret,
    claimId: context.claimId,
    claimAttemptId: context.claimAttemptId,
    cosmosAddress: context.cosmosAddress, //If pass address is configured
    _isSimulation: context._isSimulation,
    lastUpdated: context.lastUpdated,
    createdAt: context.createdAt,
    maxUses: context.maxUses,
    currUses: context.currUses,
    version: context.version
};
```

For local development, you can mock requests with the expected payload. For example,

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "pluginSecret": "...",
    "_isSimulation": false,
    "claimId": "abcxyz123",
    "claimAttemptId": "...",
    "currUses": 0,
    "maxUses": 10,
    "lastUpdated": 1800000000000,
    "createdAt": 1800000000000
  }' \
  http://localhost:3001/api/handler
```

### **Handling**

The custom logic of the plugin is left up to you. From the provided request, you can check everything you need, perform the custom logic, and more.

#### State

An important aspect to consider is how you will handle state (if applicable). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail.

If your plugin depends on whether a claim is eventually successful or not (e.g. restrict to 5 claims per user), handling state on your end may not be applicable because it cannot know for certain whether a claim is successful or not. To help such plugins, you may use the preset state functions configured by the responses (see Responses), or you can consider utilizing other already implemented plugins to do such work for you. For example, if you want to implement a query of Discord users (one claim per user) who attended an event, the one claim per user must be set and tracked on the BitBadges end.

Otherwise, you can handle state on your end. An example of this might be if you are checking a user's GitHub contributions, a contribution cannot be undone, so even if the claim fails, the plugin will still work as intended eventually. If you are handling state on your end, a design decision to consider is whether to update at user input time or claim processing time.

**Plugin Secret**

The plugin secret will be given to you upon creation of the plugin, and BitBadges will pass this to every request, so you can ensure BitBadges as the origin of the call. This is important to maintain privacy.

We also recommend taking extra precautions such as checking origin in the worst cases of data breaches or compromises on the BitBadges end.

**Private Values**

Private values are not supported by default. Private claim parameters can be configured and handled by you (see [Creation Parameters](implementing-your-plugin.md#creation-parameters) above). Anything else private should be managed and handled by you (e.g. API keys, etc). Consider setting up a proxy intermediary, if necessary.

The user inputs can be assumed to be passed through secure communication channels, but like explained above, add extra measures to protect against certain worst case scenarios.

**Specific Claim Numbers**

If your plugin needs to assign specific claim numbers, see the claim numbers preset below. Claim numbers may be used to distribute specific badges.

**Authentication**

A typical use case for a plugin is to perform authenticated logic for your users. However, plugins do not have access to BitBadges sessions and cannot implement typical session cookies. Treat BitBadges as the middleman.

The workaround for this is to give your authenticated user a unique value (such as an authorization code) only known to them. This will be passed to the claim body inputs. Upon receiving it in your backend (at claim time), you can associate the current claim to the unique value.

This approach follows the same flow as OAuth authorization codes, except with a custom claim code. You should follow all the same best practices (expiring tokens, PKCE for preventing authorization code interception attacks, and more). Note that claims may take a couple minutes for the user to complete the process, so a 30 second expiration time, for example, may be too low.

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **Response**

All responses expect a 200 success OK status code for a successful.

**Stateless Preset**

The stateless preset is simple. If we receive the 200, the plugin is successful. Nothing else is checked via the response. Everything is handled on your end (if you have state).&#x20;

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Claim Token Preset**

This preset expects a { claimToken} in the response. The claim token is a one-time use only claim code. Issuing claim tokens is left up to you. We will deny a user who attempts to use a claim token a second time. The token should not contain any "." characters (we will throw) because that messes up the state handler.

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

**State Transitions Preset**

This preset expects a { newState } in the response. If the claim is successful, we will set the current state stored in our plugin to the new state. If this option is selected, you also have access to the prior state in the request payload.

IMPORTANT: Do not assume that a successful response means a successful claim and a successful set of the new state. Think of this as a hypothetical state transition. From the prior state in the payload, this is what the new state will be **IF** the claim is successful.

Note: Ensure the JSON object keys do not contain any "." characters because that may mess up the state handler. For example, emails should be bob@abc\[dot]com rather than bob@abc.com.

**Claim Numbers Preset**

This preset expects a { claimNumber } in the response. The claim number is the claim number that will be assigned if the claim number is successful. Claim numbers are 0-based, so claimNumber === 0 is the first claim, and so on.

The prior state of the number of uses plugin will be passed via the request payload.

IMPORTANT: Only one plugin can control claim number assignment. If you select this approach, claims that use this plugin will not be compatible with any other plugin that uses the claim number preset.

Another important decision you will have to consider is whether to reuse your plugin for address lists because claim numbers do not matter for address list plugins. So basically, your plugin will function like the stateless preset for address list claims, if selected.
