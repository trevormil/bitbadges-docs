# Overview

Custom plugins are, in simple terms, just a configured HTTP request that we call upon attempting to claim. The plugin (your logic) will handle if the plugin status should be failed or successful, plus it potentially tells us how to manage the plugin state.&#x20;

We have designed plugins in a way to allow you maximum customization by letting you handle as much of the plugin logic as possible. This is a design decision as we believe the core logic of the distribution process should be decentralized and community-driven (not centralized on BitBadges servers).

**Potential Parties**

* Claim Creator - Entity creating the claim that uses the plugin
* Claiming User - End user attempting to claim
* Plugin Creator - Entity creating the plugin

**Parts of the Plugin**

* Backend Handler - All plugins have a backend handler that  we expect a 200 OK response from, along with other details depending on the configuration, at claim time.
* Claim Creator Input Handlers - The creator will need to configure public and private parameters for the specific claim, if applicable. This can be done in-site or outsourced to a configuration tool.
* User Input Handlers - The user may also need to enter inputs for the claim attempt. This can also be done in-site or outsourced to your own custom frontend.

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

**State Management**

If there is any core state required to be used, this must be managed on BitBadges side to avoid race conditions. To workaround this, the plugin will pass along expected updates IF the claim is passed (e.g. mark this one time use claim token as USED IF the claim is successful). The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail. The other way around is also true. If your plugin fails, the overall claim may still be successful (e.g. 1 out of 10 plugins must pass but yours fails).

**Published Plugins**

Plugins are private and only usable by the creator and approved users by default, but you can publish them as well. Published plugins will be displayable in the directory (after a review process) and selectable by anyone creating a claim.&#x20;

**Plugin IDs vs Instance IDs**

Plugin IDs are constant and specific to a specific plugin type. For example, the password pluginId is "password".&#x20;

Instance IDs are a unique identifier for a specific plugin in your claim. This is to handle duplicates. For example, you may have two password plugins, one with instance Id "abc" and one with "def". This is just used as a unique identifier and can really be anything. If you only have one instance of a specific type, you can name it the same as the pluginId as well.
