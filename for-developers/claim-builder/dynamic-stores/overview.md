# Overview

#### What are Dynamic Stores?

A flexible storage system on BitBadges' side for maintaining lists of:

* Email addresses
* User Crypto addresses
* User IDs / usernames names from platforms like:
  * Discord
  * GitHub
  * And more

These can be thought of as a separate store for maintaining a list of users. You then "attach" stores to their respective plugins when creating a claim. Then, instead of checking from a static list when chewcking criteria, we check from the dynamic store.

This is an alternative to custom plugins because the validation and storage is performed by BitBadges. No storage required on your end. You just update the store as you go with API calls or hooks.

<figure><img src="../../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

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

### Integration Capabilities

#### Zapier Compatibility

* Trigger-based updates
* Connect with 7000+ apps
* Automate list management from any integrated platform

<figure><img src="../../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

#### API Flexibility

Simply manage your store via the BitBadges API automatically. This can be used to create flexible flows like catch a webhook from any app -> parse email -> update email store, for example.

<figure><img src="../../../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

