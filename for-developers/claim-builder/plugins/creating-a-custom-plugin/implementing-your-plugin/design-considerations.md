# Design Considerations

### Your Plugin Status !== Claim Success Status

For processing hooks, note that your plugin may succeed, but this does not mean the overall claim will succeed. Other plugins may fail, or other stuff may go wrong.

If you receive a post-success hook, you can be sure the claim has succeeded. Or, you can use the attempt ID to verify it on your end as well,

### Asynchronous Processing

Claims are processed in an asynchronous manner. For processing hooks, your plugin may succeed, but the claim may not complete until a later time.

Best practices:

* If using state management on BitBadges end for custom plugins, design your plugin to avoid race conditions. For example, do not return the same one-time use claim token to multiple attempts. Most plugins are stateless (on BitBadges end) though and do not have to worry about this. It should always be eventually oconsistent.
* Do not depend on the BitBadges claim state like number of claims completed. It may not be up to date or real-tie at the time of your plugin's execution. Your custom parameters are okay to depend on.

This is not really applicable to post-success hooks.

### State Management

An important aspect to consider is how you will handle state (if applicable). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail. Or vice versa, your plugin may fail, but the claim succeeds. We only update state on our end if your plugin passed and was in the success path (we short-circuit OR requirements).

You have a couple options:

* Use the preset response patterns to customize how BitBadges controls state for your plugin on our end. This is eventually consistent meaning that you return an intent from your handler, and we update only upon successful claim (and in the success path).
  * A typical flow is to associate certain state with unique claim tokens and let BitBadges handle the claim tokens being marked as USED vs UNUSED.
* Manage state on your end, but be mindful of the way BitBadges processes claims.

### Reusing for On-Demand Claims

To be used with on-demand claims, the plugin must meet specific requirements and have specific properties:

* Stateless - No per-attempt state
* No User Inputs - The plugin should be able to function at any time without any custom user inputs. Note this also includes socials or connected sessions.
* Only Needs Context - The plugin should be able to function with just the contextual information passed. The context mainly includes the plugin information, claim information, and the claimee's address.

Typically, these are only possible with crypto-native plugins. For example,

* Check >1 ETH in account from address
* Check POAP ownership
* Check token ownership

Parameters are okay because they are hardcoded and not changed (e.g. set min ETH to 1).

### **Authentication / Sensitive Values**

As a design decision, we do NOT want to handle your authentication or sensitive values. Treat BitBadges as a middleman. Authentication should either be fully managed by BitBadges (identifers passed to you like email, usernames) or fully managed by you from start to finish. This applies to both the creator and the end claiming user.

If you need to make authenticated requests on behalf of the user (beyond just receving identification information), you will need to implement authentication on your end. You may let the claim creator store sensitive information in the private parameters of the plugin.

Consider a workaround such as storing any information yourself, mapping it to a token or code, giving that code to the user / creator, and having them enter it in the flow. You then use the token / code to lookup the information in your handler.

This approach follows the same flow as OAuth authorization codes, except with a custom claim code. You should follow all the same best practices (expiring tokens, PKCE for preventing authorization code interception attacks, and more).

<figure><img src="../../../../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>
