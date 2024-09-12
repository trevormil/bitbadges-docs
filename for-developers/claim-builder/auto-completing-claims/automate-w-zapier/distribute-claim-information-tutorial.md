# Distribute Claim Information Tutorial



### Overview

The first option is to distribute the necessary information to claim on the BitBadges site to users via Zapier. For example, send a unique claim code to a user when they complete this form.

The cool thing about this approach is that you do not need to communicate through crypto-native channels. This means that you do not need to know users' addresses beforehand. You can send the claim code, for example, via the Email by Zapier Plugin or Gmail plugin, and once the user receives the email, they can log on to the BitBadges site and select their address to receive the badges for when claiming.

For this approach, you do not need any communication with the BitBadges API Zapier plugin. Everything can be done via existing plugins, apps, and integrations. The downside to this approach is that since you are only distributing claim information and not initiating the claim itself, the end user always has to initiate the claim to complete the action. It is not automatic.

You may also consider using the Send Claim Alert action with the BitBadges API Zapier plugin to send secret claim information. This will send an in-app notification on the BitBadges site to the user.

See the tutorial via the link below for more information.&#x20;

### Pre-Requisite

It is assumed that you have created a claim via the BitBadges site and have got the secret information to distribute already. Otherwise, you will need to do that first.&#x20;

For the tutorial below, we use claim codes, so if you want to follow along exactly, you will need to generate a claim with claim codes.

### Tutorial

Before beginning, we want to note that this is NOT the only way to do so. You can experiment with different integrations and see what works best for your use case. This tutorial uses the following execution flow in the image below. Also, note that the tutorial is for claim codes but can be replaced for any claim information to be distributed like passwords or secret IDs.

<figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>



Step 1: Create and setup your Zap on [https://zapier.com/](https://zapier.com/). The site will walk you through it all.

Step 2: Select your trigger. Triggers are the action that initiate the automation flow. In the case above, this is a new Google form response. We leave the selection of the trigger up to you. This will depend on your intended use case.

Step 3: Setup the Storage by Zapier action with the following values. This will create a value in store that increments once every time the Zap is triggered.

It is important to note that even test runs increment the Zap, so be mindful. We recommend maybe using different keys for tests and production (e.g. "numResponses-test" vs "numResponses").

Also, note that unique stores (Accounts tab) will have blank state, so you can create a new account / store to reset the counters.

For more advanced manipulation, see [https://store.zapier.com/](https://store.zapier.com/).&#x20;

<figure><img src="../../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

Step 4: Setup a Google Sheets with the Claim Codes. The sheet should have two columns (one with an incrementing ID and one with the code value. You should have column headers as well. The codes can be copy and pasted from bitbadges.io.

<figure><img src="../../../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Step 4: Setup the Lookup Spreadsheet Row for Google Sheets action in Zapier. Select your spreadsheet. The lookup column should be your numeric ID column (not the code value). The lookup value should be your numResponses variable from the previous Storage by Zapier action.

<figure><img src="../../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

Step 5: Setup your final actions. In our case above, we are sending an email via Gmail with the code. Whatever your action is, you can insert the unique claim code to be distributed from the Lookup Row step. For example, in our email body we dynamically use the code value from the Codes column from our prior step.

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
