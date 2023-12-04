# Distribution

Once the collection is created, the badges then have to be distributed to owners. BitBadges is multi-chain meaning that any blockchain user from one ecosystem (e.g. Ethereum) can receive and send badges to and from users from another ecosystem (e.g. Cosmos).

### How to Distribute?

Distributing depends on your unique requirements and the type of collection / list you have created.

**Address Lists + Off-Chain Balances**

For address lists and badges with off-chain balances (see [here](../concepts/balances-types.md)), you manually assign who is on the list or who owns the badge. Users will never need to interact in any way with the blockchain! They will be automatically populated to a users' portfolio.

As a result, this option offers much enhanced user experience; however, there are some tradeoffs such as further centralization, less customization, and no on-chain transfer transactions. Read more [here](../concepts/balances-types.md).

**On-Chain Balances**

For on-chain balances (see [here](../concepts/balances-types.md)), all badges initially start in the Mint address, and there has to be some sort of transfer transaction from the Mint address to the recipient address. These transfers can be done in many ways dependent on your use case:

* **Manual Transfers:** The manager or other approved parties can directly transfer on the recipients' behalf. Who can transfer is clearly defined in the collection's transferability.
* **Claims:** Claims can be setup so that a user can claim the badge from the Mint address, if they meet certain criteria. Some examples include
  * **Passwords:** Set up a reusable password that must be entered to claim a badge.
  * **Codes:** Generate unique one-time use only codes that allow users to claim. Codes can be distributed using any preferred method such as **email, QR codes, social media, etc.**
  * **Whitelists / Blacklists**: Only allow specific addresses to claim.

Note the initiator of the transfer pays the transaction fees.

**Address-Based vs Code / Password-Based**

A key factor in determining distribution method is if you know your users' addresses / BitBadges usernames or not.&#x20;

If you do, you can use a whitelist, manually assign balances, or some other address-based method.

If you do not, you have two options:

1. Collect their addresses somehow (e.g. surveys) and then use an address-based method.
2. Use a password / code-based distribution method and distribute the codes that way. This can be done via typical communication methods like email, social media messaging, etc.

See [Ecosystem ](../ecosystem.md)to find a list of all distribution tools built by the BitBadges team and community. See [this tutorial](../../for-developers/tutorials/build-a-distribution-tool.md) if you would like to create and add a new tool.

**Notifying Users**

If you use an address-based approach through the BitBadges site, we will automatically trigger a notification to the intended recipients (claim alerts for whitelists and activity if a badge is transferred / assigned to them).

### Setting Up a BitBadges Address Survey

We have created a little helper tool on the BitBadges website to streamline the process of collecting your users' addresses usernames.&#x20;

To create a survey, follow the instructions below:

1. Come up with a unique survey ID. To check if it is unique and unused, visit https://bitbadges.io/addresses/survey\_ADDSURVEYIDHERE and verify it is empty. Replace ADDSURVEYIDHERE with your ID.
2. Use the tools on the Actions tab to help you collect addresses.

IMPORTANT Notes:

We are looking to expand the functionality in the future, but currently, surveys have the following properties:

1. Anyone with the survey ID can 1) see all addresses added and 2) add an address. If you need privacy, we recommend implementing your own survey and to not use this tool.&#x20;
2. Since anyone with the survey ID can add an address, you need to take action accordingly to protect it from getting leaked to untrusted parties.
3. Surveys are append-only, non-editable, and non-deletable.
