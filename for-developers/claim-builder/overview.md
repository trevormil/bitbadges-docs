# Overview

> 💡 **Note:** This documentation is intended to provide developer documentation for more advanced use cases. Most of the time, you can directly do everything in-site. Or if you need custom functionality, it is oftentimes a better approach to just self-implement the functionality you need rather than deal with the complexities of connecting with claims. LLMs, especially with MCP, are great for implementing all of this stuff. We just provide claims as an out of the box tool in-site.

BitBadges claims are designed to be a comprehensive tool directly within the site or via the API for you to custom gate your utility. Claims can be simply thought of as: **Meet criteria? -> Offer utility / rewards.** The implementation process aims to be super flexible, allowing you maximum customization.

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

### **How do claims work? What are plugins?**

Claims are made up of one or more plugins executed in order. By default, all must pass, but this can be customized. Out of the box, we offer plenty of built-in plugins and features with no code required and directly claimable by users in the site. Or, you can extend its functionality with helper tools, custom plugins, Zapier, our API, and more!

### **Get Creative**

While we do offer a ton of functionality directly in-site, your desired functionality may not be directly supported. Before considering custom implementations, get creative!

* Can you implement everything with a token and then check token ownership?
* Use claim codes or a password which can be used as a universal approach (no need for a specific app integration)
* Can your users be identified by email? Addresses? Use those plugins or dynamic stores
* Does Zapier support your approach? They have 7000+ apps and integrations natively. Pipedream?

### **How to create / manage claims?**

Claims are created and managed in the developer portal.

### **What is possible in-site?**

Most of the time, you can directly do everything without a line of code. Get creative and experiment!

* Gate URLs / Content to those who claim with the Rewards tab
* Use the Discord Role Assigner plugin to create gated channels
* And much more

### **Can claims connect to other BitBadges services?**

Claims are the universal connector. You can not only check criteria from any BitBadges service (token ownership, >100 points) but also use claims on the reward side (gate mints, award points).

<figure><img src="../../.gitbook/assets/image (394).png" alt=""><figcaption></figcaption></figure>
