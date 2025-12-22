# ❓ FAQ

### **Are smart contracts needed?**

No!  One universal standard that can support any use case with no code, no smart contracts. It is all a Cosmos SDK module reused for any token type.

### **How is compliance checked every transfer?**

The `collectionApprovals` of each token collection are checked on every transfer. These define the collection-wide transferability rules. If a transfer does not satisfy a collection approval, it will fail. Thus, by implementing compliance on the collection / token level, this is enforced every transfer no matter the app that is implementing it.

### **Is BitBadges an L1 blockchain or an L2?**

BitBadges is its own Layer-1 blockchain built with Cosmos SDK.

### **Why the registry architecture over unique smart contracts for every collection?**

In the long run, we believe a reusable standard tested 100s of times will win over unique, vulnerable contracts. The registry approach also improves scalability, consistency, and standardization. Standardization wins.

### **Are tokens ERC-721 or ERC-20 or ERC-3643 compatible?**

No. While our token standard takes inspiration from existing standards like ERC-721, our token standard has its own properties and architecture. However, they are a superset of these standards (offer all the features these standards + 1000x more), so you can use them compatibly where needed.

### **Does BitBadges support extending with smart contracts?**

Yes, our token standard is a Cosmos module. While we aim for no smart contracts to ever be needed, you can definitely still call in to our module from EVM and CosmWASM environments. Or, extend our module to call into other environments.&#x20;

### **Pricing? Affiliates?**

BitBadges charges a 0.1% fee on the protocol level on all swaps and transfers. This works as follows:

* For any x/badges transfers with IBC payments (coinTransfers), we charge in the IBC currency.
* For any x/gamm swaps, we charge a 0.1% taker fee

Normal x/badges or IBC MsgSends or transfers do not charge a fee.

As for affiliates, you are free to build protocols to charge anything on top of that.

* For x/badges, simply add another coin transfer to approvals (payout initiator 0.1% or somehting)
* For x/gamm, we have an in-built affiliates field built into the protocol. If you use the BitBadges API, we will charge a 20% fee of affiliates, but on the protocol level, there is no such charge.
