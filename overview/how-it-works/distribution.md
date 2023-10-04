# Distribution

Once the collection is created, the badges then have to be distributed to owners. **BitBadges is cross-chain meaning that any blockchain user from one ecosystem (e.g. Ethereum) can receive and send badges to and from users from another ecosystem (e.g. Cosmos).**

### How to Distribute?

For address lists, there are no transfer transactions. Upon creation, the list will be fully defined specifying all users who are on the list. They will automatically populate to a users' portfolio.

For badges, distribution can be done via your preferred method.&#x20;

**On-Chain Balances**

For on-chain balances (see [here](../concepts/balances-types.md)), there has to be some sort of transfer transaction from the Mint address to the recipient address. This can be done in two ways.

1\) The manager or creator of the collection can set up a **claim** so that recipients must enter a valid code/password or be on a whitelist or meet some other criteria to receive the badge. These are initiated by the recipient. Some examples are via **password, QR codes, e-mails, whitelists, first-come first-serve, etc.**&#x20;

2\) Or, the manager can set themselves to have admin privileges to be approved to transfer from the Mint address as desired. Thus, they can distribute the badges to whoever they want manually by directly transferring. The manager initiates these transactions.

Note that the users pretty much pay their own transaction fees for option 1) but for option 2), the manager pays all transaction fees.

**Off-Chain Balances**

For off-chain balances (see [here](../concepts/balances-types.md)), users never need to interact in any way with the blockchain to receive badges! Badges will be automatically be entered into a users' portfolio upon creation.

As a result, this option offers much enhanced user experience; however, there are some tradeoffs such as further centralization, less customization, and no on-chain transfer transactions. Read more [here](../concepts/balances-types.md).

### Questions to Answer

Before distributing, you should answer the following questions:

1. **How am I identifying my recipients?**&#x20;
   1. If you know their addresses, a whitelist is probably the best option.&#x20;
   2. If via some other method (email address, social media username, etc), it is probably best to generate unique codes / passwords and distribute codes for claiming that way.
2. **How am I going to collect all my recipients' details?** If you do not already have your recipients' details, you can collect details using any method you prefer. Some examples include:
   1. Paperform - Collect users information via an online survey form.
   2. Rafflecopter - Have users sign up for a raffle.
   3. FriendTech - Use friend.tech to associate Twitter usernames with Ethereum addresses.
   4. Or any other way!
3. **How will I notify recipients / provide them the details?**&#x20;
   1. If you create a whitelist through the BitBadges app, it will automatically create a claim alert notification next time they log in. No additional details (like codes or a password) are needed for whitelists.
   2. For distributing codes / passwords and notifying users, this can be done via many services such as Mailchimp for sending codes via email or SMS, using social media to direct message your codes, etc.

See [Ecosystem ](../ecosystem.md)to find a list of all distribution tools built by the BitBadges team and community. See [this tutorial](../../for-developers/tutorials/build-a-distribution-tool.md) if you would like to create and add a new tool.

