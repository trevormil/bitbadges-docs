# Overview

BitBadges claims are designed to be a comprehensive tool directly within the site or via the API for you to custom gate your utility. Claims can be simply thought of as: **Meet criteria? -> Offer utility / rewards**

The implementation process aims to be super flexible, allowing you maximum customization. This is why the documentation may be a little overwhelming at first. You will not need 95% of what is documented in this section. The easiest way we recommend to get started is to go to the Claim Tester in the Developer Portal and just experiment. Come back to the relevant sections when you have specific questions.&#x20;

In most cases, you actually will be able to offer everything you want to directly in-site with no code!

<figure><img src="../../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

They are the backbone of everything else in the site. They can be used with badges to gate distribution. They can be used with Sign In with BitBadges to add criteria checks to an authentication flow.  They even have native plugins for combining checks from all our services like checking badge ownership, checking points, etc.

**How to create / manage claims?**

Claims are created and maintained through the BitBadges site.&#x20;

* Linked to a badge / list? Maintain them through their respective interfaces
* Standalone? Manage through the developer portal

Go to the Create tab to get started. Use the Claim Tester for experimenting.

**What are plugins?**

Claims are made up of one or more plugins executed in order. For a claim to be successful by default, all plugins must pass. Or, you can customize the success logic. As you see above, the claim has 3 plugins (number of uses, IP, and email) which all must pass.

Out of the box, we offer plenty of built-in plugins and features with no code required and directly claimable by users in the site. Or, you can extend its functionality with helper tools, custom plugins, Zapier, our API, and more!

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

