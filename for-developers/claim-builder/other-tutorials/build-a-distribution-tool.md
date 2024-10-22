# Build a Distribution Tool

Distribution tools are helper tools that streamline the process of distributing badges upon creation. An example tool might be to fetch and distribute badges to the the addresses of all users who attended an event. Other example ideas include distributing via e-mail, QR codes, location, etc.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

### **Have an idea for a tool?**

When building a distribution tool, you have to answer a couple questions:

**Do I actually have the recipients' blockchain addresses?**

If you will have access to the recipients' actual blockchain addresses, your tool can simply generate a list of addresses and distribute via a whitelist.

If you can generate a list of addresses separated by a new line such as:

```
bb1...
bb...
bb...
0x123...
```

Then, this list can simply be copied into the "Add Recipients" field on the BitBadges website under the "Whitelist" distribution method.

You can also use alternative tools or build your own tools for submitting the transaction.

**Or, do I identify them in some other way (e.g. Twitter usernames, emails, etc)?**

If you identify your users in some other way, you will probably have to implement your tool by using codes or a password. The codes and passwords should then be distributed to the users, sot hey can claim with their preferred address. You will need to do the following:

1. Generate a code/password based claim with N codes (see below)
2. Use your tool to manually distribute the codes / password to the correct users

### **Building Your Tool**

**Step 1: Build**

Implement the functionality of your tool.

**Step 2: Develop an Instructions Page**

Develop a detailed instructions page for what your tool offers and how to use it. How does it work? What to enter? What to copy? What to provide? Pre-requisites? And so on.

Note you will need to design for compatibility of certain features offered on the BitBadges website (if you want compatibility). Feel free to reach out for advice and help if needed.

**Step 3: Get It Added to the BitBadges Website / Docs**

If compatible, reach out to us to add it to the tools array in the frontend directory! Please provide us a brief description of the tool, an image for your logo, and the URL of the tool.

### Examples

[https://bitbadges-email-distribution-tool-trevormil.vercel.app/](https://bitbadges-email-distribution-tool-trevormil.vercel.app/) ([Code](https://github.com/BitBadges/bitbadges-email-distribution-tool))

<figure><img src="../../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>
