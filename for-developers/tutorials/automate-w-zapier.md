# Automate w/ Zapier

**What is Zapier?**

Zapier is an online automation tool that connects your favorite apps, such as Gmail, Slack, Mailchimp, and more than 2,000 others. You can automate repetitive tasks with workflows known as Zaps. A Zap connects two or more apps to automate part of your business or personal tasks. A Zap is created using a trigger and one or more actions. A trigger is an event in an app that starts the Zap. After a trigger occurs, Zapier automatically completes an action—or series of actions—in another app. This seamless connection between apps enables complex tasks to be completed automatically, saving time and improving productivity.

See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

**How does Zapier fit in with BitBadges?**

The cool thing about distribution on BitBadges (claim codes, claim passwords, secret IDs) is that they are not crypto-native, so you can customize your experience with many different apps and integrations.&#x20;

**Example Use Cases w/ BitBadges and Zapier**

#### **1. Registration with BitBadges**

* **Trigger:** A user registers for an event through your website. This can be gated by Blockin authentication or Sign in with BitBadges to check badge ownership as well. This can be done with the Webhooks by Zapier plugin.
* **Action:** Zapier sends the user a BitBadges claim code to generate a unique badge as proof of registration.&#x20;

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

#### **2. Course Completion Certificates**

* **Trigger:** A user completes an online course.
* **Action:** Zapier communicates with your course platform and issues the user a unique claim code or secret ID for the certification badge or ceritification secret.

Remember, these are just examples to spark ideas. The possibilities are endless with Zapier and BitBadges integration. Familiarize yourself with both platforms to make the most out of your automations.



### Tutorial

Before beginning, we want to note that this is NOT the only way to do so. You can experiment with different integrations and see what works best for your use case. This tutorial uses the following execution flow in the image below. Also, note that the tutorial is for claim codes but can be replaced for any vlaue to be distributed like passwords or secret IDs.

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>



Step 1: Create and setup your Zap on [https://zapier.com/](https://zapier.com/). The site will walk you through it all.

Step 2: Select your trigger. Triggers are the action that initiate the automation flow. In the case above, this is a new Google form response. We leave the selection of the trigger up to you. This will depend on your intended use case.

Step 3: Setup the Storage by Zapier action with the following values. This will create a value in store that increments once every time the Zap is triggered.

It is important to note that even test runs increment the Zap, so be mindful. We recommend maybe using different keys for tests and production (e.g. "numResponses-test" vs "numResponses").

Also, note that unique stores (Accounts tab) will have blank state, so you can create a new account / store to reset the counters.

For more advanced manipulation, see [https://store.zapier.com/](https://store.zapier.com/).&#x20;

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

Step 4: Setup a Google Sheets with the Claim Codes. The sheet should have two columns (one with an incrementing ID and one with the code value. You should have column headers as well. The codes can be copy and pasted from bitbadges.io.

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Step 4: Setup the Lookup Spreadsheet Row for Google Sheets action in Zapier. Select your spreadsheet. The lookup column should be your numeric ID column (not the code value). The lookup value should be your numResponses variable from the previous Storage by Zapier action.

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

Step 5: Setup your final actions. In our case above, we are sending an email via Gmail with the code. Whatever your action is, you can insert the unique claim code to be distributed from the Lookup Row step. For example, in our email body we dynamically use the code value from the Codes column from our prior step.

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>
