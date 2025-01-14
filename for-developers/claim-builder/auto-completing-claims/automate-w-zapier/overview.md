# Overview

Zapier BitBadges Integration Invite Link: [https://zapier.com/developer/public-invite/203980/ce0b2c34275cfdb61ef1aac1ed0aa38c/](https://zapier.com/developer/public-invite/203980/ce0b2c34275cfdb61ef1aac1ed0aa38c/)

**What is Zapier?**

Zapier is an online automation tool that connects your favorite apps, such as Gmail, Slack, Mailchimp, and more than 2,000 others. You can automate repetitive tasks with workflows known as Zaps. A Zap connects two or more apps to automate part of your business or personal tasks. A Zap is created using a trigger and one or more actions. A trigger is an event in an app that starts the Zap. After a trigger occurs, Zapier automatically completes an action—or series of actions—in another app. This seamless connection between apps enables complex tasks to be completed automatically, saving time and improving productivity.

See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).

<figure><img src="../../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

**How does Zapier fit in with BitBadges?**

The process of claiming a badge can be customized with Zapier to integrate any app you would like.&#x20;

When automating claims with Zapier, you will follow the approach of upon doing something (custom trigger), perform an action  (claim a badge, distribute a code, add to dynamic store).

**Example Use Cases w/ BitBadges and Zapier**

#### **1. Registration with BitBadges**

* **Trigger:** A user registers for an event through your website. The registration process is gated by Blockin authentication or Sign in with BitBadges to check badge ownership as well. This can be done with the Webhooks by Zapier plugin.
* **Action:** Zapier claims a unique badge as proof of registration on behalf of the user.

#### **2. Course Completion Certificates**

* **Trigger:** A user completes an online course.
* **Action:** Zapier communicates with your course platform and issues the user a unique claim code or secret ID for the claiming process.

Remember, these are just examples to spark ideas. The possibilities are endless with Zapier and BitBadges integration. Familiarize yourself with both platforms to make the most out of your automations.

<figure><img src="../../../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

## **Execution Time**

Note that Zaps are asynchronous, so the time at which a Zap is triggered and eventually succeeds (or fails) will probably matter.

If you are auto-completing claims with Zapier, this ensures the Zap will succeed before the claim is completed. You can also take other measures to ensure the Zap succeeds before the claim is completed like issuing claim codes via the Zap.

You may also consider triggering the Zap during the execution of the claim or after its success. The easiest way to do this is with the custom trigger (Get Claim Successes) of the BitBadges Zapier integration.&#x20;

Alternatively though, for executing during it, this can be done with custom plugins or the Custom Validate URL plugin supported natively in-site. Or for custom post-success logic, you can use the Success Webhook plugin in-site or the sucess webhooks features when creating a custom plugin.

## **Error Handling**

You should also account for the fact that Zaps can partially execute. For example, if you have a Zap with 10 plugins and it fails on the 8th plugin, the Zap will not be considered a success but 8/10 plugins would be executed already. There is no rollback feature.

Similarly, if you are triggering a Zap during a claim's execution, there is no guarantee that a claim is successful because other plugins might fail.

## Triggers

The custom trigger step is left up to you to implement from any of the 7000+ app integrations.&#x20;

## **Actions**

With the claim or action step of the automated flow, you have a couple options depending on your use case.

{% content-ref url="distribute-claim-information-tutorial.md" %}
[distribute-claim-information-tutorial.md](distribute-claim-information-tutorial.md)
{% endcontent-ref %}

{% content-ref url="automatic-claim-tutorial.md" %}
[automatic-claim-tutorial.md](automatic-claim-tutorial.md)
{% endcontent-ref %}
