# Creating a Plugin

Custom plugins are, in simple terms, just a configured HTTP request that we call upon attempting to claim. The plugin will handle if the claim attempt should be denied or not.&#x20;

To create and publish your plugin, go to [https://bitbadges.io/developer](https://bitbadges.io/developer).

## **Request**

The outgoing request (from BitBadges to your plugin) will be made up of multiple aspects that can be configured.

* **Hardcoded Inputs:** Inputs passed to every call regardless of configuration. These are set by the plugin creator.
* **Public Parameters:** Inputs configured by the claim creator per claim that are also to be publicly available to users as well (e.g. max uses per user).
* **Private Parameters:** Inputs configured by the claim creator per claim that are not to be shown to the user.
* **Plugin Secret:** A plugin secret value that you can use to verify BitBadges as the origin of the call.
* **Context:** Some additional context about the claim attempt (user adddress, claim ID, etc).
* **Additional User Context:** We also allow certain values to be passed, such as the user's signed in socials (Discord, GitHub, etc) usernames, if requested. These will be the { username, id } for the user only (no access tokens or session details).

For POST, PUT, and DELETE requests, we pass the values over the body. For GET, we pass them over the GET params. You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.). Make sure it is the desired type as well (i.e. GET vs POST vs DELETE vs PUT).

```typescript
 const body = {
    ...customBody,
    ...publicParams,
    ...privateParams,
    ...apiCall?.hardcodedInputs.map((input) => ({ [input.key]: input.value })),
    claimId: context.claimId,
    cosmosAddress: apiCall?.passAddress ? context.cosmosAddress : null,
    discord: apiCall?.passDiscord ? adminInfo.discord : null,
    twitter: apiCall?.passTwitter ? adminInfo.twitter : null,
    github: apiCall?.passGithub ? adminInfo.github : null,
    google: apiCall?.passGoogle ? adminInfo.google : null,
    email: apiCall?.passEmail ? adminInfo.email : null,
    pluginSecret: pluginDoc.pluginSecret
};
```

## **Handling**

The custom logic of the plugin is left up to you. From the provided request, you can check everything you need, perform the custom logic, and more.&#x20;

### State

An important aspect to consider is how you will handle state (if applicable). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail.&#x20;

If your plugin depends on whether a claim is eventually successful or not (e.g. restrict to 5 claims per user), handling state on your end may not be applicable because it cannot know for certain whether a claim is successful or not. To help such plugins, you may use the preset state functions configured by the responses (see Responses), or you can consider utilizing other already implemented plugins to do such work for you. For example, if you want to implement a query of Discord users (one claim per user) who attended an event, the one claim per user must be set and tracked on the BitBadges end.

Otherwise, you can handle state on your end. An example of this might be if you are checking a user's GitHub contributions, a contribution cannot be undone, so even if the claim fails, the plugin will still work as intended.

### Private Inputs

There are a couple things that you can do to ensure keeping things private with BitBadges plugins.&#x20;

**Plugin Secret**

The plugin secret will be given to you upon creation of the plugin, and BitBadges will pass this to every request, so you can ensure BitBadges as the origin of the call.

**Private Values**

Private values can be configured via the private parameters (set by the claim creator) or hardcoded inputs (set by the plugin creator).&#x20;

However, although these values are kept private, you should keep a few things in mind:

* You are entrusting BitBadges (a centralized service) with these private values. You should NOT use these variables for storing important secrets like API keys, etc that should be maintained on your end. If necessary, create a proxy intermediary that maintains private values.
* These values, especially the hardcoded inputs, are not easily rotatable.

Consider adding extra challenges and security to your execution flows which assume that communication is intercepted or BitBadges is compromised.&#x20;

**Body**

The user inputs can also be assumed to be kept private, but like above, add extra measures to protect against certain worst case scenarios.

**Authentication**

A typical use case for a plugin is to perform authenticated logic for your users. However, plugins do not have access to BitBadges sessions and cannot implement typical session cookies. Treat BitBadges as the middleman.

The workaround for this is to give your authenticated user a unique value only known to them that they can supply in the claim body inputs. Upon receiving it, you can associate the current claim to the user who created the unique value.&#x20;

This approach follows the same flow as OAuth authorization codes, except with a custom claim code. You should follow all the same best practices (expiring tokens, PKCE for preventing authorization code interception attacks, and more). Note that claims may take a couple minutes for the user to complete the process, so a 30 second expiration time, for example, may be too low.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## **Response**

All responses expect a 200 success OK status code for a successful.

**Stateless Preset**

The stateless preset is simple. If we receive the 200, the plugin is successful. Nothing else is checked via the response.

**Username / ID Preset**

This preset expects a { username, id } to be returned with the response. The ID is the account ID (that is constant) and the username is the display name.&#x20;

Using the configured parameters, we will then keep track of state on our end regarding how many times the user has claimed and deny them if they exceed the limit. This is equivalent to the other socials plugins implemented.

**Claim Token Preset**

This preset expects a { claimToken} in the response. The claim token is a one-time use only claim code. Managing claim tokens is left up to you. We will deny a user who attempts to use a claim token a second time.

## **Further Customization**

In the future, we are looking to expand on the customization options to allow you to build your plugin exactly how you want. If you would like further customization (custom UI components, custom state functions, etc), reach out to us to help you get started.
