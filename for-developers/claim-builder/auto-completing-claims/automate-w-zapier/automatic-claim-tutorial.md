# Automatic Claim Tutorial

### Overview

The other option is to trigger claims automatically with Zapier. You will configure the Zap to automatically complete the claim for the user upon a custom trigger. For example, upon purchasing an item, auto-send them a purchased item badge.

To do this, you **MUST** get the users' crypto addresses somehow before the action is executed. This can be beforehand or somehow obtained during the duration of the Zap. We leave this up to you. If you cannot obtain users' addresses, this approach will not work.

We want to note that functionality is slightly different for badges with on-chain balances as opposed to off-chain badges or address lists.

* On-Chain: The check and complete claim action will **RESERVE** the right for the user to complete the claim. However, it does not actually automatically trigger anything on the blockchain. This is because such a transaction requires a signature from the recipient. Thus, the user still has to go to the BitBadges site and complete the claim process, although the reservation process is automatic.
* Off-Chain and Other Claims: For badges with off-chain balances or other claim types, there is no reservation process. The claims are automatically completed. For off-chain badge claims, this means the badges will be auto-distributed. For address lists, this means the address will be automatically appended to the list.

You will use the BitBadges API Zapier plugin with the Complete Claim action to perform the final claim completion. This approach takes special configuration explained in the tutorial below to ensure the claim process is correct and only executable by the Zap. See the tutorial for more information.

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Create the Claim

The first step is to create the claim via the Bitbadges site; however, note that the configuration of the claim must be correct to ensure correct behavior of the claim process and allow Zapier to communicate. Select the Zapier approach when creating, and it should guide you through the process.

Note: Many in-site plugins may become incompatible due to the user not completing in-site. However, you gain access to any custom trigger from 7000+ apps on Zapier.

<figure><img src="../../../../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

## Tutorial

Example walkthrough of a custom Zap. Customize to your use case.

Step 1: Create and setup your Zap on [https://zapier.com/](https://zapier.com/). The site will walk you through it all.

Step 2: Select and configure your trigger. Triggers are the action that initiate the automation flow. In this cases, this is a new Udemy course being completed. We leave the selection of the trigger up to you. This will depend on your intended use case.

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Step 3: Configure the Complete Claim by BitBadges integration. The password and other config parameters will be constant and obtained when creating the claim. However, the address may be fetched from prior integrations or manually provided. This is up to you.

<figure><img src="../../../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

You can get the claim information from the form when you are creating the claim.

Step 4: Simulate / Test

You may use the simulation feature to test that your claim communicates and will pass without actually executing the action.

Note that behind the scenes, this is just a simulation (does not actually complete the claim) if sent manually from the test step. The claim will actually be completed for successful Zaps once live.

Step 5: Track Progress

For most use cases, just submitting the claim is typically adequate. However, you can also track it with the other BitBadges action in Zapier (Get Claim Attempt Status). You will pass the ID received from the submission to this. Note that we use a queue system so it may take some time to officially process.

And, it is as easy as that!
