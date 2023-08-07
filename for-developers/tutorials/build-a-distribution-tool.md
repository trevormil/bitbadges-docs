# Build a Distribution Tool

Distribution tools are helper tools that streamline the process of distributing badges upon creation. An example tool might be to fetch and distribute badges to the the addresses of all users who attended a Twitter Spaces.&#x20;

Other example ideas include distributing via e-mail, QR codes, location, etc.

**Pre-Readings**

[Claims](broken-reference) - Know the difference between whitelist claims and code/password based claims.

### **Have an idea for a tool?**

When building a distribution tool, you have to answer a couple questions:

**Do I actually have the recipients' blockchain addresses?**&#x20;

If you will have access to the recipients' actual blockchain addresses, your tool can simply generate a list of addresses and distribute via a whitelist.

If you can generate a list of addresses separated by a new line such as:

```
cosmos1...
cosmos...
cosmos...
0x123...
```

Then, this list can simply be copied into the "Add Recipients" field on the BitBadges website under the "Whitelist" distribution method.

You can also use alternative tools or build your own tools for submitting the transaction.

**Or, do I identify them in some other way (e.g. Twitter usernames, emails, etc)?**

If you identify your users in some other way, you will probably have to implement your tool by using codes or a password. You will need to do the following:&#x20;

1. Generate a code/password based claim with N codes (see below)
2. Use your tool to manually distribute the codes / password to the correct users

**How should everything be stored?**

If you use the BitBadges website, the codes, passwords, and whitelist details for claims are stored on the BitBadges servers. However, you can also generate all codes yourself and store them / distribute them.

Your two options:

* Store everything on the BitBadges centralized servers (recommended). This is the most straightforward approach but also adds another centralized point of failure and trust for the creator (codes will be known both by BitBadges centralized servers plus your tool).
  * Your tool should be designed in a way that users can simply use the "Mint" or "Update Collection" form on the BitBadges website.
* Generate and store everything yourself. If you generate everything (transactions and codes) yourself, you can completely bypass the BitBadges servers for storage.&#x20;
  * Note you will need to design for compatibility of certain features offered on the BitBadges website (if you want compatibility).
  * Note that you will need to generate the transaction contents yourself, but you can have the user copy and paste it onto the website using the advanced broadcast page (see Broadcasting and Submitting Txs).
  * Feel free to reach out for advice and help if needed.

### **Building Your Tool**

**Step 1: Build**&#x20;

Implement the functionality of your tool.

**Step 2: Develop an Instructions Page**

Develop a detailed instructions page for what your tool offers and how to use it. How does it work? What to enter? What to copy? What to provide? Pre-requisites? And so on.

**Step 3: Get It Added to the BitBadges Website**

If compatible, contact us to add it to on the BitBadges website! Please provide us a brief description of the tool, a URL for your logo, the URL to find instructions, and where you would like it to be placed on the BitBadges website.

