# ❓ FAQ

### **Are smart contracts needed?**

No! All tokens are no-code with tons of functionality out of the box. One universal standard that can support any use case with no code, no smart contracts. It is all a Cosmos SDK module reused for any token type.

### **How is compliance checked every transfer?**

The collectionApprovals of each token collection are checked on every transfer. These define the collection-wide transferability rules. If a transfer does not satisfy a collection approval, it will fail. Thus, by implementing compliance on the collection / token level, this is enforced every transfer no matter the app that is implementing it.

{% content-ref url="../for-developers/bitbadges-sdk/common-snippets/get-unhandled-approvals.md" %}
[get-unhandled-approvals.md](../for-developers/bitbadges-sdk/common-snippets/get-unhandled-approvals.md)
{% endcontent-ref %}

### **Is BitBadges an L1 blockchain or an L2?**

BitBadges is its own Layer-1 blockchain built with Cosmos SDK.

### **Why the registry architecture over unique smart contracts for every collection?**

In the long run, we believe a reusable standard tested 100s of times will win over unique, vulnerable contracts. The regirstry approach also improves scalability, consistency, and standardization.

### **Are tokens ERC-721 or ERC-20 compatible?**

While our token standard takes inspiration from existing standards like ERC-721, our token standard has its own properties and architecture. However, they are a superset of these standards (offer all the features these standards + 1000x more).

Cosmos modules (like our token standard) can be called from EVM environments using the Cosmos EVM module + precompiles. This also could allow you to offer seamless compatibility with all your favorite interfaces.

### **How does pairing with IBC currencies work?**

Simply, each approval can specify native Cosmos coin transfers to trigger if used. For example, if the mint approval is used and specifies a 5 USDC payment to Bob, it will trigger it. This supports any bank denomination.

{% content-ref url="../x-badges/concepts/approval-criteria/usdbadge-transfers.md" %}
[usdbadge-transfers.md](../x-badges/concepts/approval-criteria/usdbadge-transfers.md)
{% endcontent-ref %}

### **How does Cosmos / IBC wrapping work?**

While our token standard and all state is scoped to the module, we allow you to define "wrapper paths" to convert freely to / from x/bank (IBC) denominations. Once wrapped, you could get all the benefits of IBC + the Cosmos ecosystem with interoperability and more.

This is done by sending to / from a special wrapper address that is auto-generated. It is abstracted as a typical transfer in our standard. Thus, this opens up the possibilities to control who, when, how often wrapping is allowed.

{% content-ref url="../x-badges/concepts/cosmos-wrapper-paths.md" %}
[cosmos-wrapper-paths.md](../x-badges/concepts/cosmos-wrapper-paths.md)
{% endcontent-ref %}

### **How does BitBadges L1 compare to other interoperability protocols?**

BitBadges doesn't "pull" data from other chains - all relevant data is stored on the BitBadges chain, simplifying development. However, it supports users and wallets from any chain, allowing multi-chain transfers (e.g., Bitcoin users can transfer tokens to Solana users).

Note that we support Cosmos / IBC wrapping for true interoperability where you may need.

### **Does BitBadges support extending with smart contracts?**

Yes, our token standard is a Cosmos module. While we aim for no smart contracts to ever be needed, you can definitely still call in to our module from EVM and CosmWASM environments.
