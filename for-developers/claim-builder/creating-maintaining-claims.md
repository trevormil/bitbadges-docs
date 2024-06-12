# Creating / Maintaining Claims

Claims are created and maintained through the BitBadges site. You will get the chance to create and update claims when creating / updating a badge collection or address list.  You can also use the [BitBadges API](../bitbadges-api/tutorials/) for more programmatic CRUD; however, this is typically not necessary as everything can also be done directly in-site.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Design Considerations

**Collecting Addresses vs Allowing User Selection**

* **Pre-Collection of Addresses**: Automatic claiming on behalf of users may require knowing users' crypto addresses beforehand, necessitating a pre-collection step.&#x20;
* **User Select at Claim Time**: Or, if users claim on the BitBadges site, they can specify their own address when claiming.

**Ensuring Correct Actions Behavior**

* Claims are approvals to perform an action, which means the underlying action must be configured properly and executable as well (e.g., sufficient balances, on-chain approvals). A successful claim is nothing without a successful action.

**Automatic Claims**&#x20;

* If you are planning to auto complete claims behind the scenes via the API or via Zapier, note that you will not be making requests signed in as the user (unless you have a really advanced custom implementation). This means that the "Signed In with BitBadges" plugin needs to be disabled.
* **Important Note**: Disabling "Signed In with BitBadges" allows any user to claim on another's behalf. You need to prevent this case with other criteria. The recommended approach (mandatory for Zapier) is to use a secret password with the password plugin (e.g., Zapier integration) that is to be specified by the service performing the auto-claim.

**Designing Claims for On-Chain Balances**&#x20;

* As explained with the claim actions, claims for on-chain badges use a "reserve" system where a successful claim reserves the right to claim on-chain via a one-time use code. The claim code is then used via the underlying on-chain approval. Thus, it is important to align the criteira of the approval and the claim.
* Protect against cases where the BitBadges off-chain claim can be reserved but not executed on-chain or vice versa. This may happen if the claim criteria does not match the on-chain approval.
  * For example, if you create a BitBadges claim that can be reserved by Bob, but Bob is not approved on-chain, Bob cannot actually receive the badges.
  * This is commonly a problem for approved addresses and transfer/claim times not aligning up.
