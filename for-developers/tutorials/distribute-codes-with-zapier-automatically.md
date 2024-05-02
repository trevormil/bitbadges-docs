# Distribute Codes with Zapier Automatically

The cool thing about codes / password based claims is that they are not crypto-native, so they can be integrated with comonly used tools like Zapier and more.

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

### Tutorial

Step 1: Create and setup your Zap on [https://zapier.com/](https://zapier.com/). The site will walk you through it all.

Step 2: Select your trigger. Triggers are the action that initiate the automation flow. In the case above, this is a new Google form response. We leave the selection of the trigger up to you. This will depend on your intended use case.

Step 3: Setup the Storage by Zapier action with the following values. This will&#x20;

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

Step 4: Setup a Google Sheets with the Claim Codes. The sheet should have two columns (one with an incrementing ID and one with the code value. You should have column headers as well. The codes can be copy and pasted from bitbadges.io.

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Step 4: Setup the Lookup Spreadsheet Row for Google Sheets action in Zapier. Select your spreadsheet. The lookup column should be your numeric ID column (not the code value). The lookup value should be your numResponses variable from the previous Storage by Zapier action.

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

Step 5: Setup your final actions. In our case above, we are sending an email via Gmail with the code. Whatever your action is, you can insert the unique claim code to be distributed from the Lookup Row step. For example, in our email body we dynamically use the code value from the Codes column from our prior step.

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>
