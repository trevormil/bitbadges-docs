# Build a Distribution Tool

Distribution tools are helper tools that streamline the process of distributing badges upon creation. An example tool might be to fetch and distribute badges to the the addresses of all users who attended an event. Other example ideas include distributing via e-mail, QR codes, location, etc.

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

If you identify your users in some other way, you will probably have to implement your tool by using codes or a password. The codes and passwords should then be distributed to the users, sot hey can claim with their preferred address. You will need to do the following:&#x20;

1. Generate a code/password based claim with N codes (see below)
2. Use your tool to manually distribute the codes / password to the correct users

**How should everything be stored?**

If you use the BitBadges website, the codes, passwords, and whitelist details for claims are stored on IPFS and the BitBadges servers. However, you can also generate everything yourself and store them / distribute them.

Your two options:

* Store everything on the BitBadges centralized servers (recommended). This is the most straightforward approach. Your tool should be designed in a way that users can simply use the "Mint" or "Update Collection" form on the BitBadges website.&#x20;
  * For example, your tool simply tell users to copy and paste specific addresses into the form. Or, tell them to generate codes using the form, download them, and give them to the tool for distribution.
* Generate and store everything yourself. If you generate everything (transactions and codes) yourself, you can completely bypass the BitBadges servers for storage.&#x20;
  * Note you will need to design for compatibility of certain features offered on the BitBadges website (if you want compatibility). See the expected format of the JSON file for merkleChallenge.uri [here](ipfs://QmSCGwqBofFt69iGzKERw6z27Eszchd39AoPNLLGB18exE) or use the [MerkleChallengeDetails](https://bitbadges.github.io/bitbadgesjs/packages/utils/docs/interfaces/MerkleChallengeDetails.html) type from the SDK.
    * Note this JSON file will be public, so DO NOT include sensitive information like passwords or unhashed preimages (the actual codes) here. Whitelisted addresses are okay.&#x20;
    * See here for a sample [MsgUpdateCollection](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/setup/bootstrapped-collections/7\_100\_person\_whitelist.json#L176).
  * You could have the user copy and paste the generated transaction onto the website using the advanced broadcast page (/dev/broadcast) (see Broadcasting and Submitting Txs). This outsources all the broadcasting, signing, etc. to an interface, and you simply need to generate the Msg contents.
  * Feel free to reach out for advice and help if needed.

### **Building Your Tool**

**Step 1: Build**&#x20;

Implement the functionality of your tool.

**Step 2: Develop an Instructions Page**

Develop a detailed instructions page for what your tool offers and how to use it. How does it work? What to enter? What to copy? What to provide? Pre-requisites? And so on.

**Step 3: Get It Added to the BitBadges Website**

If compatible, contact us to add it to on the BitBadges website! Please provide us a brief description of the tool, an image for your logo, and the URL of the tool.



### Examples

[https://bitbadges-email-distribution-tool-trevormil.vercel.app/](https://bitbadges-email-distribution-tool-trevormil.vercel.app/) ([Code](https://github.com/BitBadges/bitbadges-email-distribution-tool))
