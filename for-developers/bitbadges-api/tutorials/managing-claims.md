# Managing Claims

Claims can also be managed via the BitBadges API.

<pre class="language-typescript"><code class="lang-typescript"><strong>await BitBadgesApi.createClaim({ ... });
</strong><strong>await BitBadgesApi.updateClaim({ ... });
</strong><strong>await BitBadgesApi.deleteClaim({ ... });
</strong></code></pre>

Typically, you will not need this as claims can be managed and updated via the BitBadges site. However, for more advanced cases, you may want to use the API. All require the proper permissions and authorizations.

### **Creating Claims**

Claims can be created through the BitBadges API; however, this may get a little tricky because we may have to coordinate with the blockchain.

**Address List Claims**

For address list claims, this is straight forward. You just specify the list ID to create the claim for and you are good (as long as you are the creator).

**Collections w/ On-Chain Balances**

For claims that map to an on-chain approval, you will need to make sure the claim ID == the challenge tracker ID of your on-chain approval. This will tie the two together inherently. The ID must be unique from any other claim, and you must use the same address to create the claim and blockchain transaction (that creates the approval). This address also needs to be the manager address.&#x20;

You must create the claim first (reserves the ID behind the scenes) and then the approval after. This does not require the collection ID, so this can be done before the collection is even created.

**Collections w/ Off-Chain Balances**

For claims for collections with off-chain balances, the collection must be created first. Then, you can simply specify the collection ID, and we will check that you are the manager.

### **Updating Claims**

With updating claims, you can change the plugins, metadata, or even micro-manage the state of each plugin. Each plugin will have a **resetState** or **newState** field that can be used to set the state to what you want to. Resetting it will go back to the default, blank state. Specify newState will set it to exactly what is provided (thus, it is important it is configured properly). See the documentation for each plugin for how state is managed.

### **Deleting Claims**

Claims can also be deleted. However, note that they are soft deleted, meaning they can be reinstated with the same ID and same state at a later time.
