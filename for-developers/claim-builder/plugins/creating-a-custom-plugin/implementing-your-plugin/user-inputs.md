# Custom Inputs

### Handling User / Creator Inputs

If your plugin requires inputs from the claiming user or claim creator, you can do this in the BitBadges site (recommended) or via a window.postMessage from a custom frontend / tool. All inputs will be passed along to your handler via the payload. If none are provided, we assume there is nothing needed to be passed.

<figure><img src="../../../../../.gitbook/assets/image (338).png" alt=""><figcaption></figcaption></figure>

Note to be compatible with Zapier (and possibly API auto-claiming) or on-demand claims, user inputs are typically not allowed (because the user is not manually initiating anything).

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

You can also select to automatically pass supported identifying details about the user (e.g. crypto addresses, Discord, X, GitHub, etc). Note no access tokens or auth details are passed along so private, authorized requests are not possible with this information.

We will authenticate the user on our end where needed, and you can use their identifying information to execute queries (e.g. public GitHub contributions). The only one that may not be verified in some cases is the claiming address. This depends on the configuration (the user must have the Signed In to BitBadges plugin). If you need to make sure that the user is signed in, check the **isAddressSignedIn** field. This will be true if the claiming user is signed in as the claiming address.

**Address Formats:** The plugin payload will include both **bitbadgesAddress** (bb-prefixed Cosmos address) and optionally **ethAddress** (0x-prefixed Ethereum address) if the user is using an Ethereum wallet. Both addresses represent the same account.

<figure><img src="../../../../../.gitbook/assets/image (388).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/image (389).png" alt=""><figcaption></figcaption></figure>
