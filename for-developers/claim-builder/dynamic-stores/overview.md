# Overview

#### What are Dynamic Stores?

A flexible storage system on BitBadges' side for maintaining lists of:

* Email addresses
* User Crypto addresses
* User IDs / usernames names from platforms like Discord, etc

These can be thought of as a separate store for maintaining a list of users. You then "attach" stores to a claim and gate the claim by checking if a user is in the store. You control it. We store it. Update it:

* Manually in-site in the developer portal
* Automatically w/ API calls

```typescript
await BitBadgesApi.performStoreAction(...)
await BitBadgesApi.performBatchStoreAction(...)
```

* Automatically w/ Zapier - Listen to 7000+ apps and add users to store in no-code

To get started, go to the Developer Portal. This will walk you through the process of creating, attaching, and adding data to the store.

Once your store is created, you can add users / data to it. Note that we add via a queue-based approach, so the data may take a couple moments to populate.

**Attach to a Claim**

The interface should walk you through the process of attaching it to a claim:
1. Directly in claim builder, you should see your stores in the templates section
2. In the stores tab, click Create Claim for a one-click create a claim which is auto-configured
3. Add the corresponding plugin (email for email stores) and add the store in the parameters select

**Store ID and Store Secret**

When creating your store, you will get a store ID and secret. These are to be provided by the API / Zapier when managing data. You can also manage the store without the secret if you are signed in with the owner address.

#### Core Advantages

**1. Reusability**

* Not tied to specific plugins or claims
* Can be shared across multiple verification processes

**2. Dynamic Management**

* Real-time updates on the go (dynamic instead of static)
* For example, as users check in to an event, you can dynamically add them to the store which would then make the plugin pass for those users dynamically

**3. Outsourced Storage**

* As opposed to a traditional plugin where you maintain a validation endpoint and potentially store data, this is outsourced to us.
* The roles are flipped. We handle the storage and validation, and you just send update hooks as they come in.

**4. Not Address Dependent**

* All accounting for claims and other BitBadges services is done primarily with crypto addresses. For example, to complete a claim, you need to specify a claiming address. However, many apps and services do not have such a link from user ID on the app -> crypto address.&#x20;
  * This approach allows you to not need to worry about addresses and only the app identifiers / emails. BitBadges handles the rest.&#x20;

<figure><img src="../../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>
