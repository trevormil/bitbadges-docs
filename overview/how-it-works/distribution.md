# Distribution / Collection

BitBadges is multi-chain meaning that any blockchain user from one ecosystem (e.g. Ethereum) can receive and send badges to and from users from another ecosystem (e.g. Cosmos).

See [here](balances-types.md) to learn about the pros and cons of the different balance types (on-chain vs off-chain).

## How is everything distributed?

Distributing depends on your unique requirements and the type of collection / list you have created. Typically, the creator sets up the rules for how everything is distributed. **To see how a specific collection / list is distributed, you must visit the page for that collection.**

### **Address Lists + Badges with Off-Chain Balances - Manual Assignment**

For address lists and badges with off-chain balances (see [here](balances-types.md)), some entity (typically the creator or manager) will manually assign who is on the list or who owns the badge. Users will never need to interact in any way with the blockchain! They will automatically show up in a users' portfolio without the user needing to do anything.

For badges with off-chain balances, the assignment process can also be further customized with custom logic implementation, such as registering on the collection's official website to receive a badge or being a paid subscriber (off-chain) to earn a badge. This custom logic can also utilize non-blockchain data, such as private databases or standard non-crypto payment methods. See [here](balances-types.md) to learn about the pros and cons of the different balance types (on-chain vs off-chain). **Always interact with third parties at your own risk!**

### **On-Chain Balances**

There are a few ways that badges can be distributed with on-chain balances.

Typically, all badges initially start in the Mint address, and there has to be some sort of transfer transaction from the Mint address to the recipient address. These transfers can be done in many ways dependent on your use case, as explained below.

**Manual**

The manager or other approved parties can directly initiate a transfer forcefully on the recipients' behalf. Who is permitted to transfer is clearly defined in the collection's transferability.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Claims**

Claims can be setup so that a user can claim the badge from the Mint address, if they meet certain criteria. Note the initiator of the transfer pays the transaction fees. Some examples include

* **Passwords:** Set up a reusable password that must be entered to claim a badge.
* **Codes:** Generate unique one-time use only codes that allow users to claim.
* **Whitelists / Blacklists**: Only allow specific addresses to claim.

Codes / passwords can be distributed using any preferred method such as **email, QR codes, social media, etc.** Note these methods do not have to be crypto-native. Consider using widely used tools, such as Mailchimp, Discord, or Twilio to help you distribute codes / passwords, collect user addresses, and communicate with users. **Although, always use third-party tools at your own risk.** For developers, see [Badge Distribution](../distribution-tools-integrations.md) for some common integration examples.

Below are some ways that we natively support distributing on the BitBadges website.

* **Direct Claim Links:** Provide a unique link to the claim page. Anyone with the unique link can go and claim the badge. The password / code will be auto-populated for them. These links can be distributed however (QR codes, email, etc). Note that claiming requires a transaction, so wallets must be handy at claim time.&#x20;
* **Save for Later Links:** For codes / passwords, we also offer Save for Later links which allow you to save the code / password for later (typically via a mobile device). These are often used when wallets are not handy in the moment.
* **Claim Alerts:** BitBadges allows you to send in-app claim alerts to users notifying them to claim the badges with the link / instructions for how to do so.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Starting Balances**

Another option is to define default starting balances. For example, everyone starts with x1 of a specific badge. This is typically not used with the normal minting processes explained above.

For this option, ALL addresses will start out with the defined balances.

### **Notifications / Alerts**

A key factor in determining distribution method is if you know your users' addresses / BitBadges usernames or not. If you do, you can use a whitelist, manually assign balances, or some other address-based method. If you do not, you have two options:

1. Collect their addresses somehow (e.g. surveys) first and then use an address-based method.
2. Use a password / code-based distribution method and distribute the codes that way. This can be done via typical communication methods like email, social media messaging, etc.

You ultimately know your users best. We leave the communication and alerting step up to you.

**Claim Alerts and Ecosystem Tools**

If you use an address-based approach through the BitBadges site, we will automatically trigger a notification to the intended recipients (claim alerts for whitelists and activity if a badge is transferred / assigned to them).

See [Ecosystem ](../ecosystem/)to find a list of all distribution tools built by the BitBadges team and community. See [this tutorial](../../for-developers/tutorials/build-a-distribution-tool.md) if you would like to create and add a new tool.
