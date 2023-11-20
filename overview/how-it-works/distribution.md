# Distribution

Once the collection is created, the badges then have to be distributed to owners. BitBadges is multi-chain meaning that any blockchain user from one ecosystem (e.g. Ethereum) can receive and send badges to and from users from another ecosystem (e.g. Cosmos).

### How to Distribute?

Distributing depends on your unique requirements and the type of collection / list you have created.

**Address Lists + Off-Chain Balances**

For address lists and badges with off-chain balances (see [here](../concepts/balances-types.md)), you manually assign who is on the list or who owns the badge.&#x20;

Users will never need to interact in any way with the blockchain! They will be automatically populated to a users' portfolio.

As a result, this option offers much enhanced user experience; however, there are some tradeoffs such as further centralization, less customization, and no on-chain transfer transactions. Read more [here](../concepts/balances-types.md).

**On-Chain Balances**

For on-chain balances (see [here](../concepts/balances-types.md)), all badges initially start in the Mint address, and there has to be some sort of transfer transaction from the Mint address to the recipient address. These transfers can be done in many ways dependent on your use case:

* **Manual Transfers:** The manager or other approved parties can directly transfer on the recipients' behalf. Who can transfer is clearly defined in the collection's transferability.
* **Claims:** Claims can be setup so that a user can claim the badge from the Mint address, if they meet certain criteria. Some examples include
  * **Passwords:** Set up a reusable password that must be entered to claim a badge.
  * **Codes:** Generate unique one-time use only codes that allow users to claim. Codes can be distributed using any preferred method such as **email, QR codes, social media, etc.**
  * **Whitelists / Blacklists**: Only allow specific addresses to claim.

Note the initiator of the transfer pays the transaction fees.

### Questions to Answer

Before distributing, you should answer the following questions:

1. **How am I identifying my recipients?** **Do I know their addresses?**
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

