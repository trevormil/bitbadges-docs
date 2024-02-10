# 🔀 Distribution Tools

## Building Blocks

**Off-Chain Balances**

For off-chain balances, the creator has complete control over how to allocate badge balances. Since these are off-chain, they can also access non-blockchain data to further enhance the allocation logic.

**On-Chain Balances**

On the BitBadges site, we natively support the building blocks (whitelists, passwords, claim codes) and have interfaces for you to share the following:

* **Plaintext Codes / Passwords:** For codes / passwords, you can share/copy/download the codes yourself and distribute how you would like.
* **Direct Claim Links:** Provide a unique secret link to the claim page. These links can be distributed however (QR codes, email, etc). Note that claiming requires a transaction, so wallets must be handy at claim time.
* **Save for Later Links:** For codes / passwords, we also offer Save for Later links which allow you to save the code / password for later (typically via a mobile device). These are often used when wallets are not handy in the moment.
* **Claim Alerts:** BitBadges allows you to send in-app claim alerts to users notifying them to claim the badges with the link / instructions for how to do so.

<figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Distribution Tools

However, the building blocks are to be combined with distribution tools to further enhance the distribution experience. For example, you might create a code-based claim on the BitBadges website but distribute the claim links in a variety of ways such as via QR codes, email, Discord, or SMS.

BitBadges aims to have a vast ecosystem of community-built distribution tools. Our goal is that users will have thousands of options to choose from built by different teams, each offering their own unique niche.

Here, we provide documentation for common distribution integrations / tools as a reference. Let us know if you want to add a tool or tutorial here. **Always use third party tools at your own risk!** You know your users best. The best method is the way you typical communicate with users.

Distribution may involve:

* Collecting addresses or usernames of your users to implement whitelists / off-chain balances
* Distributing codes / passwords that are used to claim badges
* Notifying users they are on an whitelist
* Notifying users for how to earn / collect this badge

### Address Collection Surveys

* BitBadges - Create an address list with the Create tab and set Survey Mode to true. This will create a list, and users can add addresses to the survey given a unique link. For more customization, see the developer documentation [here](../for-developers/tutorials/custom-address-surveys.md).
* Forms - Set up a simple form for users to enter their desired recipient address

### Email

* Email Helper Tool - [https://bitbadges-email-distribution-tool-trevormil.vercel.app/](https://bitbadges-email-distribution-tool-trevormil.vercel.app/) ([Code](https://github.com/BitBadges/bitbadges-email-distribution-tool))
* Mailchimp - Distribute codes / passwords via the use of merge tags

{% embed url="https://mailchimp.com/help/all-the-merge-tags-cheat-sheet/" %}

### SMS

* Twilio - Twilio is a popular platform for sending SMS messages programmatically. You can use Twilio's API to send unique codes or passwords via SMS to your users.

### Social Media

* Distribute codes / passwords or alert users of whitelist opportunities via social media communication (DMs, announcements, tweets, etc).

### Web2 Payments

* Stripe - Stripe is a reliable payment gateway that you can integrate into your distribution platform. Maybe you want to distribute a badge to those who are paid subscribers.

