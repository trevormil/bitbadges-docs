# Design Considerations

### State Management

An important aspect to consider is how you will handle state (if applicable). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail.&#x20;

Because of this, state that is core to the claim must be managed on BitBadges end to avoid race conditions. Use the preset response patterns to customize how BitBadges controls state for your plugin. You can also consider utilizing other already implemented plugins to do such work for you. For example, if you want to implement a query of Discord users (one claim per user) who attended an event, the one claim per user must be set and tracked on the BitBadges end.

A typical flow is to associate certain state with unique claim tokens and let BitBadges handle the claim tokens being marked as USED vs UNUSED.

### **Authentication / Sensitive Values**

As a design decision, we do NOT recommend or supporting private values like API keys or authentication session details. We also do not pass any session cookies or details to plugins. Anything like this should be managed on the plugin end. Treat BitBadges as the middleman.

Consider workarounds such as:

-   Intermediate handler proxies
-   Passing authorization codes rather than the sensitive values themselves (similar to the OAuth flow)

Although everything will be communicated through secure communication channels, you should treat BitBadges as a semi-trusted middleman. Add extra measures to protect against certain worst case scenarios, or add an additional layer of abstraction to avoid letting BitBadges know anything secret.&#x20;

**Example**

Give your authenticated user (the claiming user) a unique value (such as an authorization code) only known to them. Pass this via the user's claim inputs. Upon receiving it in your backend (at claim time), you can use the value to fetch the authentication session and corresponding details.

This approach follows the same flow as OAuth authorization codes, except with a custom claim code. You should follow all the same best practices (expiring tokens, PKCE for preventing authorization code interception attacks, and more). Note that claims may take a couple minutes for the user to complete the process, so a 30 second expiration time, for example, may be too low.

<figure><img src="../../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

### **Snapshots vs Dynamic Requests**

There are a couple common implementation patterns for plugins. If possible, we recommend the first option.

1. Snapshot + User Auth - At creation time, configure the parameters (potentially with an authorized request from the creator) and no future requests on behalf of the creator need to be made. At claim time, you could use user authentication to check criteria. For example,
    1. Configure all approved emails -> verify user email at claim time
    2. Configure the Twitch channel name -> use user Twitch authorization to check subscription
2. Dynamic - Some plugins, however, may need to execute creator authorized requests upon each claim. For this, you will need to cache the creator details or somehow make it so that you will have authorization from the creator during all claim times. Note that you should consider all possibilities like access token expirations -> refresh functionality.

### Reusing for Non-Indexed (On-Demand) Claims / Balances&#x20;

Pre-Reading: [Claim Actions](../../claim-actions.md)

If your plugin does not require user inputs, you may be able to reuse it for non-indexed claims. Such plugins must be stateless, require no user inputs, and can function with only the contextual information passed. See [here](../../claim-actions.md) for more information.
