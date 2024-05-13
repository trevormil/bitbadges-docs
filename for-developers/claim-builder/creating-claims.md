# Creating Claims

Currently, claims can only be created and maintained through the BitBadges site (not through the API). You will get the chance to create and update claims when creating / updating a badge collection or address list.&#x20;

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Plugins

You can mix and match multiple plugins to create your claim. ALL plugins for a claim need to pass for a successful claim.

### **Designing the Claim**

* Claims are approvals (aka the right to claim or perform an action). The underlying action must also be successful to ensure correct behavior (i.e. sufficient balances, on-chain approvals if applicable, etc). &#x20;
* Designing claims for on-chain balances takes extra consideration. We use a "reserve" system where a successful claim reserves the right to claim on-chain via a one-time use only code behind the scenes. **You need to protect against cases where the claim can be reserved but not actually executed on-chain or vice versa.** This includes:
  * Ensuring addresses must be approved both for the claim and on-chain approval
  * Ensuring claim times align
  * And so on.
* If you are planning to execute claims on users behalf automatically behind the scenes, **you need to setup the claim in a way that you can do so.** For more information, see the Auto-Completing Claims pages.
  * The easiest way is to setup a secret password with the password plugin only known by you and use that to claim. This is the approach for the Zapier integration.
  * This also means that you cannot enable the "Signed In with BitBadges" plugin, unless you are getting all your users to authorize you (really advanced and extra).  However, it is IMPORTANT to note that disabling Signed In with BitBadges means that ANY user can claim on another user's behalf. You must prevent this case with other criteria, such as the password example above.



