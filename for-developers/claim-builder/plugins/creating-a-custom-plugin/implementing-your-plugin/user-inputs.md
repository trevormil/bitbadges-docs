# Custom Inputs

### Handling User / Creator Inputs

If your plugin requires inputs from the claiming user or claim creator, you can do this in the BitBadges site (recommended) or via a window.postMessage from a custom frontend / tool. All inputs will be passed along to your handler via the payload. If none are provided, we assume there is nothing needed to be passed.

<figure><img src="../../../../../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

Note: For on-demand claims or API auto-completion, user inputs are typically not allowed (because the user is not manually initiating anything).

**Option 1: Completely In-Site**

When creating the plugin, configure the expected schemas. We will prompt the users / creators to enter such information in-site via a form. All outsourced to BitBadges.

<figure><img src="../../../../../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

**Option 2: Custom Frontend**

Consider creating your own frontend that the user will be redirected to via the redirect URL.You will need to combine approaches. This typically follows a code approach. For example, handle secure stuff on your end -> grant an authorization / claim code -> have user add it directly in the site -> use the code for whatever.

**Supported Schema Types**

Primitives: String, Number, Boolean

Date: UNIX millisecond timestamp

URL: Stringified URL

### Identifying the Claiming User

You can select to automatically pass the user's address to your plugin endpoint. No access tokens or auth details are passed along.

The claiming address may not be verified (signed in) depending on the configuration. If you need to make sure that the user is signed in, check the **isAddressSignedIn** field.

**Address Formats:** The plugin payload will include both **bitbadgesAddress** (bb-prefixed Cosmos address) and optionally **ethAddress** (0x-prefixed Ethereum address) if the user is using an Ethereum wallet. Both addresses represent the same account.

<figure><img src="../../../../../.gitbook/assets/image (388).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (389).png" alt=""><figcaption></figcaption></figure>
