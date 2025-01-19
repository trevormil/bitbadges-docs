# Design Considerations

### Your Plugin Status !== Claim Success Status

An important aspect to consider when implementing your plugin is that your plugin status is not representative of the overall claim status.

* Your plugin might pass but others might fail -> The claim fails
* Your plugin fails but another plugin succeeds (ex: 1 out of 10 must pass) succeeds -> The claim succeeds with custom success logic

### Asynchronous Processing

An important concept to understand when building plugins is that BitBadges processes plugins asynchronously before the claim is fully processed. Typically, there will only be a couple seconds between processing times, but claim state is not guaranteed to stay the same between your individual plugin processing and claim processing.

**However, this means that you should NOT depend on the overall claim state in any form when implementing your processing hook logic (i.e. the state on BitBadges' end like number of claims processed).**

You may manage your own state on your end, and we have settings for you to set state in an eventually consistent manner on BitBadges end (see handler response formats below).

This mainly applies for processing hooks. For success hooks, you can assume that the respective attempt has succeeded already. Parameters are okay to depend on. We will pass the parameters of your plugin to you via the handler. If you need other plugins' parameters, you may query them via the API, but ensure the claim versions match to avoid race conditions.

### State Management

An important aspect to consider is how you will handle state (if applicable). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail. Or vice versa, your plugin may fail, but the claim succeeds.

We only update state on our end if your plugin passed and was in the success path (we short-circuit OR requirements).

You have a couple options:

* Use the preset response patterns to customize how BitBadges controls state for your plugin on our end. This is eventually consistent meaning that you return an intent from your handler, and we update only upon successful claim (and in the success path).
  * A typical flow is to associate certain state with unique claim tokens and let BitBadges handle the claim tokens being marked as USED vs UNUSED.
* Manage state on your end, but be mindful of the way BitBadges processes claims.

### Reusing for Non-Indexed (On-Demand) Claims / Balances

Pre-Reading: [Claim Actions](../../../claim-actions.md)

To be used with on-demand claims, the plugin must meet specific requirements and have specific properties:

* Stateless - No per-attempt state
* No User Inputs - The plugin should be able to function at any time without any custom user inputs. Note this also includes socials or connected sessions.
* Only Needs Context - The plugin should be able to function with just the contextual information passed. See [here](../../../claim-actions.md) for more information. The context mainly includes the plugin information, claim information, and the claimee's address.

<figure><img src="../../../../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

Typically, these are only possible with crypto-native plugins. For example,

* Check >1 ETH in account from address
* Check POAP ownership
* Check badge ownership

Parameters are okay because they are hardcoded and not changed (e.g. set min ETH to 1).

### **Authentication / Sensitive Values**

As a design decision, we do NOT want to handle authentication or sensitive values. Treat BitBadges as an untrusted middleman. Authentication should either be fully managed by BitBadges (as it is with core plugins) or fully managed by you. We also do not pass any session cookies or details for any socials requested. If you need authenticated scopes, that is left up to you on your end.

We also do not pass any session cookies or details to plugins. Anything like this should be managed on the plugin end. Consider workarounds such as:

* Intermediate handler proxies
* Passing authorization codes rather than the sensitive values themselves (similar to the OAuth flow)
* Technically you can configure private parameters to be entered by claim creator, but we recommend not using these for sensitive stuff like API keys.

Although everything will be communicated through secure communication channels, you should treat BitBadges as a middleman. Add extra measures to protect against certain worst case scenarios, or add an additional layer of abstraction to avoid letting BitBadges know anything secret.

**Example**

Give your authenticated user (the claiming user) a unique value (such as an authorization code) only known to them. Pass this via the user's claim inputs. Upon receiving it in your backend (at claim time), you can use the value to fetch the authentication session and corresponding details.

This approach follows the same flow as OAuth authorization codes, except with a custom claim code. You should follow all the same best practices (expiring tokens, PKCE for preventing authorization code interception attacks, and more). Note that claims may take a couple minutes for the user to complete the process, so a 30 second expiration time, for example, may be too low.

<figure><img src="../../../../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### **Snapshots vs Dynamic Requests**

There are a couple common implementation patterns for plugins.&#x20;

If possible, we recommend the first option.

1. Snapshot + User Auth - At creation time, configure the parameters (potentially with an authorized request from the creator) and no future requests on behalf of the creator need to be made. At claim time, you could use user authentication to check criteria. For example,
   1. Configure all approved emails -> verify user email at claim time
   2. Configure the Twitch channel name -> use user Twitch authorization to check subscription
2. Dynamic - Some plugins, however, may need to execute creator authorized requests upon each claim. For this, you will need to cache the creator details or somehow make it so that you will have authorization from the creator during all claim times. Note that you should consider all possibilities like access token expirations -> refresh functionality.
