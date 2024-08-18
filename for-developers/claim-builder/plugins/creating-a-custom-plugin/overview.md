# Overview

Custom plugins are, in simple terms, just a configured HTTP request that we call upon attempting to claim. The plugin (your logic) will handle if the claim attempt should be denied or successful, plus potentially tells us how to manage the plugin state. All plugins must pass for a claim attempt to be successful.

We have designed plugins in a way to allow you maximum customization by letting you handle as much of the plugin logic as possible. This is a design decision as we believe the core logic of the distribution process should be decentralized and community-driven (not centralized on BitBadges servers). Think of plugins not as handling the actual logic but more as an API connecting your handling logic to BitBadges claims.

To create, publish, and maintain your plugin, go to [https://bitbadges.io/developer](https://bitbadges.io/developer) and use the Plugins tab.&#x20;

Published plugins will be displayable in the directory (after a review process) and selectable by anyone creating a claim. You can also choose not to publish and keep it restricted to only be used in claims by you and invited users.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Reusing for Non-Indexed Balances&#x20;

Pre-Reading: [Claim Actions](../../claim-actions.md)

If your plugin does not require user inputs, you may be able to reuse it for non-indexed balance assignment. Such plugins must be stateless, require no user inputs, and can function with only the contextual information passed.

### **Further Customization**

In the future, we are looking to expand on the customization options to allow you to build your plugin exactly how you want. If you would like further customization (custom UI components, other presets to add, custom state functions, etc), reach out to us.

### Template

```typescript
//TODO: Fill in missing information
const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  try {
    //Step 1: Handle the request payload from the plugin
    const body = req.body; //We assume the plugin sends the payload in the body of the request (change this for GET)
    const { priorState, claimId, pluginSecret, cosmosAddress, _isSimulation, lastUpdated, createdAt } = body;
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

```

###
