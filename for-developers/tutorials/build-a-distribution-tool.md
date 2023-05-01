# Build a Distribution Tool

Distribution tools are helper tools that streamline the process of distributing badges for creators. An example tool might be to fetch and distribute the addresses of all users who attended a Twitter Spaces.&#x20;

Other ideas for tools include distributing by:

* Location
* E-Mail
* QR Codes



**Pre-Readings**

[Claims](../need-to-know/claims.md) - Know the difference between whitelist claims and code/password based claims.

### **Have an idea for a tool?**

When building a distribution tool, you have to answer a couple questions:

**Do I actually have the recipients' blockchain addresses? Or, do I identify them in some other way (e.g. Twitter usernames, emails, etc)?**

If you will have access to the recipients' actual blockchain addresses, your tool can simply generate a copyable list of addresses separated by a new line such as:

```
cosmos1...
cosmos...
cosmos...
0x123...
```

Then when creating the badge, the creator can simply copy and paste the addresses into the "Add Recipients" field on the BitBadges website. The user must select the "Whitelist" distribution method on the BitBadges website when minting.



If you identify your users in some other way, you will probably have to implement your tool by using codes. The execution flow is the following :&#x20;

1. Generate a code/password based claim with N codes
2. Have them provide you with the codes or password if using the BitBadges servers (see below). This can be done by clicking the copy icon after finalizing the claim. If codes are being copied, they will be in the following JSON format:

```
{
    "codes": ["...", ...],
    "codeRoot" : "",
}
```

1. Use your tool to manually distribute the codes / password to the correct users



**How should the codes be stored?**

By default, the codes, passwords, and whitelists for claims are stored on the BitBadges servers. This is the functionality when the user clicks the "Whitelist" or "Codes" distribution option when minting on the BitBadges website.

If your tool uses codes / password, you have two options:

* Store them on BitBadges servers and have the creator provide you with this information. This is the most straightforward approach but also adds another centralized point of failure and trust for the creator (codes are known both by BitBadges centralized servers plus your tool).
* Generate everything yourself. If you generate everything yourself, you can completely bypass the BitBadges servers for storage. This can be done by the "JSON" distribution option when minting on the BitBadges website. You will have the user copy/paste a JSON object, specifying the **transfers** and **claims** fields for the MessageMsgNewCollection.
  * Note if this option is chosen but you still want your users to be able to claim on the BitBadges website, you will need to make everything compatible with the site.

```typescript
{
  "transfers": [{
    "balances": [...],
    "toAddresses": [...]
  }],
  "claims" : [{
    ...
  }]
}
```

See [here for more info](https://bitbadges.github.io/bitbadgesjs/packages/transactions/docs/interfaces/MessageMsgNewCollection.html).





### **Building Your Tool**

**Step 1: Build**&#x20;

Implement the functionality of your tool.

**Step 2: Develop an Instructions Page**

Develop a detailed instructions page for what your tool offers and how to use it.&#x20;

* What distribution method to choose on the BitBadges website (whitelist, codes, etc)?
* What to enter? What to copy? What to provide to your tool?
* And so on.

**Step 3: Get It Added to the BitBadges Website**

Contact us to add it to on the BitBadges website! Please provide us a brief description of the tool, a URL for your logo, the URL to find instructions, and where you would like it to be placed on the BitBadges website.

