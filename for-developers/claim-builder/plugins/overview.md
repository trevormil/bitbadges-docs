# Overview

Plugins are HTTP-based extensions that let you add custom logic to claims. When a claim is executed, BitBadges sends a request to your plugin's endpoint, and your plugin responds to indicate success or failure.

1. Create your plugin in the [developer portal](https://bitbadges.io/developer)
2. Set up your handler at the specified URL / method. The payload includes context about the attempt plus the user's address and any custom inputs.
   1. Check the pluginSecret to verify BitBadges as the origin of the request if this is important
3. Return the expected response that was configured (e.g. 200 OK)

<figure><img src="../../../.gitbook/assets/image (28) (1).png" alt=""><figcaption></figcaption></figure>

```typescript
const handlePlugin = async (req: NextApiRequest, res: NextApiResponse) => {
  //Step 1: Handle the request payload from the plugin
  const body = req.body; //For POST
  const { _isSimulation, claimId, pluginSecret, bitbadgesAddress, ethAddress, lastUpdated, createdAt } = body;
  const { ...otherCustomProvidedInputs } = body;

  //Step 1.5: Catch simulations if applicable. Don't run success logic on a dry run
  // if (_isSimulation) return doDryRunStuffOnly();

  //Step 2: Verify BitBadges as origin by checking plugin secret is correct
  const YOUR_PLUGIN_SECRET = '';
  if (pluginSecret !== YOUR_PLUGIN_SECRET) {
    return res.status(401).json({ message: 'Invalid plugin secret' });
  }

  //Step 3: Implement your custom logic here. Consider checking the plugin's creation / last updated time to implement version control.

  //Step 4: Return expected responses
  return res.status(200).json({});
};

export default handlePlugin;
```

We have designed plugins in a way to allow you maximum customization by letting you handle as much of the plugin logic as possible. This is a design decision as we believe the core logic of the distribution process should be decentralized and community-driven (not centralized on BitBadges servers).

**Potential Parties**

* Claim Creator - Entity creating the claim that uses the plugin
* Claiming User - End user attempting to claim
* Plugin Creator - Entity creating the plugin

**Timing of Requests**

Plugins are processing hooks: sent during execution and can affect the overall outcome of the claim depending on the response.

We also allow users to simulate (dry run) their claim attempts. Plugins are expected to handle these as necessary. These are all configurable in the creation process in the developer portal.

**Parts of the Plugin**

* Backend Handler (Your API) - All plugins have a backend handler that we expect a 200 OK response from, along with other details depending on the configuration, at claim time.
* Claim Creator Input Handlers (Public / Private Parameters) - The creator will need to configure public and private parameters, if applicable.
* User Input Handlers (Custom Input Body) - The user may also need to enter inputs for the claim attempt.

<figure><img src="../../../.gitbook/assets/image (120) (1).png" alt=""><figcaption></figcaption></figure>

**State Management**

If there is any core state required to be used, this must be managed on BitBadges side to avoid race conditions. To workaround this, the plugin will pass along expected updates IF the claim is passed (e.g. mark this one time use claim token as USED IF the claim is successful).

The golden rule here is that a successful response from your plugin DOES NOT mean the overall claim attempt was successful. Other plugins might fail. The other way around is also true. If your plugin fails, the overall claim may still be successful (e.g. 1 out of 10 plugins must pass but yours fails).

**Plugin IDs vs Instance IDs**

Plugin IDs are constant and specific to a specific plugin type. For example, the password pluginId is "password".

Instance IDs are a unique identifier for a specific plugin in your claim. This is to handle duplicates. For example, you may have two password plugins, one with instance Id "abc" and one with "def". This is just used as a unique identifier and can really be anything. If you only have one instance of a specific type, you can name it the same as the pluginId as well.

## Supported Plugins

### Core Plugins (Built-In)

These are built into BitBadges and require no external setup:

| Plugin ID | Description |
|-----------|-------------|
| `codes` | Gate claims with one-time use claim codes |
| `password` | Gate claims with a shared password / secret |
| `numUses` | Limit the total number of successful claims |
| `transferTimes` | Restrict when claims can be completed |
| `initiatedBy` | Restrict which addresses can initiate the claim |
| `whitelist` | Gate claims to a specific address list |
| `halt` | Temporarily halt all claims |
| `anonymous` | Allow anonymous claiming (no address required) |

### Plugin-Based (Custom Plugins)

These are pre-built plugins available by plugin ID:

| Plugin Base ID | Description |
|----------------|-------------|
| `min-badge` | Require minimum badge/token ownership |
| `must-own-badges` | Require ownership of specific badges |
| `url-clicker` | Require visiting a specific URL |
| `custom-instructions` | Display custom instructions to the user |
| `satisfies-claim` | Require completion of another claim |
| `username-set` | Require the user to have a username set |

### Your Own Custom Plugins

For any logic not covered above, create your own custom plugin in the [developer portal](https://bitbadges.io/developer). See [Creating a Custom Plugin](creating-a-custom-plugin/) for the full guide.
