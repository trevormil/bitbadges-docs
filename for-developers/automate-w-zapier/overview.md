# Overview

**What is Zapier?**

Zapier is an online automation tool that connects your favorite apps, such as Gmail, Slack, Mailchimp, and more than 2,000 others. You can automate repetitive tasks with workflows known as Zaps. A Zap connects two or more apps to automate part of your business or personal tasks. A Zap is created using a trigger and one or more actions. A trigger is an event in an app that starts the Zap. After a trigger occurs, Zapier automatically completes an action—or series of actions—in another app. This seamless connection between apps enables complex tasks to be completed automatically, saving time and improving productivity.

See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

**How does Zapier fit in with BitBadges?**

The process of claiming a badge can be customized with Zapier to integrate any app you would like.  When automating claims with Zapier, you will follow the approach of upon doing something (custom trigger), claim a badge (action).&#x20;

**Example Use Cases w/ BitBadges and Zapier**

#### **1. Registration with BitBadges**

* **Trigger:** A user registers for an event through your website. The registration process is gated by Blockin authentication or Sign in with BitBadges to check badge ownership as well. This can be done with the Webhooks by Zapier plugin.
* **Action:** Zapier claims a unique badge as proof of registration on behalf of the user.&#x20;

#### **2. Course Completion Certificates**

* **Trigger:** A user completes an online course.
* **Action:** Zapier communicates with your course platform and issues the user a unique claim code or secret ID for the certification badge or ceritification secret.

Remember, these are just examples to spark ideas. The possibilities are endless with Zapier and BitBadges integration. Familiarize yourself with both platforms to make the most out of your automations.

## Triggers

The custom trigger step is left up to you to implement from Zapier's 6000+ apps.&#x20;

## **Actions**

With the claim or action step of the automated flow, you have a couple options depending on your use case.

**Distribute Claim Information**

The first option is to distribute the necessary information to claim on the BitBadges site to users via Zapier. For example, send a unique claim code to a user when they complete this form.

The cool thing about this approach is that you do not need to communicate through crypto-native channels. This means that you do not need to know users' addresses beforehand. You can send the claim code, for example, via the Email by Zapier Plugin or Gmail plugin, and once the user receives the email, they can log on to the BitBadges site and select their address to receive the badges for when claiming.

For this approach, you do not need any communication with the BitBadges API Zapier plugin. Everything can be done via existing plugins, apps, and integrations. The downside to this approach is that since you are only distributing claim information and not initiating the claim itself, the end user always has to initiate the claim to complete the action. It is not automatic.

You may also consider using the Send Claim Alert action with the BitBadges API Zapier plugin to send secret claim information. This will send an in-app notification on the BitBadges site to the user.

See the tutorial via the link below for more information.&#x20;

{% content-ref url="distribute-claim-information-tutorial.md" %}
[distribute-claim-information-tutorial.md](distribute-claim-information-tutorial.md)
{% endcontent-ref %}

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Automatic Claiming**

The other option is to trigger claims automatically. You will configure it to automatically complete the claim for the user upon a custom trigger. For example, upon purchasing an item, auto-send them a purchased item badge.

To do this, you must get the users' crypto addresses somehow before the action is executed. This can be beforehand or somehow obtained during the duration of the Zap. We leave this up to you. Consider using the BitBadges Address List feature on the site. If you cannot obtain users' addresses, this approach will not work.



We want to note that functionality is slightly different for badges with on-chain balances as opposed to off-chain badges or address lists.

* On-Chain: The check and complete claim action will **RESERVE** the right for the user to complete the claim. However, it does not actually automatically trigger anything on the blockchain. This is because such a transaction requires a signature from the recipient. Thus, the user still has to go to the BitBadges site and complete the claim process, although the reservation process is automatic.
* Off-Chain and Address Lists: For badges with off-chain balances, there is no reservation process. The claims are automatically completed. For off-chain badge claims, this means the badges will be auto-distributed. For address lists, this means the address will be automatically appended to the list.

You will use the BitBadges API Zapier plugin with the Complete Claim action to perform the final claim completion. This approach takes special configuration explained in the tutorial below to ensure the claim process is correct and only executable by the Zap. See the tutorial for more information.

{% content-ref url="automatic-claim-tutorial.md" %}
[automatic-claim-tutorial.md](automatic-claim-tutorial.md)
{% endcontent-ref %}
