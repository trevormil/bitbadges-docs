# Automatic Claim Tutorial

### Overview

The other option is to trigger claims automatically. You will configure it to automatically complete the claim for the user upon a custom trigger. For example, upon purchasing an item, auto-send them a purchased item badge.

To do this, you **MUST** get the users' crypto addresses somehow before the action is executed. This can be beforehand or somehow obtained during the duration of the Zap. We leave this up to you. Consider using the BitBadges Address List feature on the site. If you cannot obtain users' addresses, this approach will not work.

We want to note that functionality is slightly different for badges with on-chain balances as opposed to off-chain badges or address lists.

* On-Chain: The check and complete claim action will **RESERVE** the right for the user to complete the claim. However, it does not actually automatically trigger anything on the blockchain. This is because such a transaction requires a signature from the recipient. Thus, the user still has to go to the BitBadges site and complete the claim process, although the reservation process is automatic.
* Off-Chain and Address Lists: For badges with off-chain balances, there is no reservation process. The claims are automatically completed. For off-chain badge claims, this means the badges will be auto-distributed. For address lists, this means the address will be automatically appended to the list.

You will use the BitBadges API Zapier plugin with the Complete Claim action to perform the final claim completion. This approach takes special configuration explained in the tutorial below to ensure the claim process is correct and only executable by the Zap. See the tutorial for more information.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Create the Claim

The first step is to create the claim via the Bitbadges site; however, note that the configuration of the claim must be correct to ensure correct behavior of the claim process and allow Zapier to communicate.

**No Signed In with BitBadges Plugin**

&#x20;This should be disabled to allow Zapier to claim on behalf of others without needing to authenticate. In other words, whoever is claiming can specify the intended recipient without actually needing to authenticate as this recipient.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Gate with Custom Password**

However, it is IMPORTANT to note that not requiring SIWBB relaxes the restriction for everyone. Thus, you will have to gate the claim in other ways to ensure that it **only** allows Zapier to claim on users' behalf. The easiest way to do this is with a unique password. You will then configure Zapier to use this password whenever it tries to claim. This allows claims from Zapier to pass but claims from anyone else not to (because noone else knows the password).

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

**Other Plugins**

You may still configure other criteria to be checked as well (e.g. one use per recipient address, check min $BADGE, etc.) However, be aware that anything requiring custom user inputs or actions may not be able to be implemented or must be handled additionally.

For example, to check an address' GitHub contributions, they need to Sign In with GitHub, but the Zap cannot sign in the user with GitHub. Thus, this plugin is not usable.&#x20;

However, certain ones like checking badge ownership or checking minimum $BADGE owned do not require any user inputs and will be fine to use.

Note that you are also integrating with Zapier, so you can also chain anyone of the 6000+ app integrations into your automated Zap. Oftentimes, you can find a plugin that does exactly what the one on BitBadges would do.

## Tutorial

Example walkthrough of a custom Zap. Customize to your use case.

Step 1: Create and setup your Zap on [https://zapier.com/](https://zapier.com/). The site will walk you through it all.

Step 2: Select and configure your trigger. Triggers are the action that initiate the automation flow. In this cases, this is a new Udemy course being completed. We leave the selection of the trigger up to you. This will depend on your intended use case.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Step 3: Configure the Complete Claim by BitBadges integration. The password and claim ID will typically be constant. However, the address may be fetched from prior integrations or manually provided. This is up to you.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

And, it is as easy as that!
