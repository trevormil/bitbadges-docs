# Overview

Zapier BitBadges Integration: [https://zapier.com/apps/bitbadges/integrations](https://zapier.com/apps/bitbadges/integrations)

**What is Zapier?**

Zapier is an online automation tool that connects your favorite apps, such as Gmail, Slack, Mailchimp, and more than 2,000 others. You can automate repetitive tasks with workflows known as Zaps. A Zap connects two or more apps to automate part of your business or personal tasks. A Zap is created using a trigger and one or more actions. A trigger is an event in an app that starts the Zap. After a trigger occurs, Zapier automatically completes an action—or series of actions—in another app. This seamless connection between apps enables complex tasks to be completed automatically, saving time and improving productivity.

See all supported apps here: [https://zapier.com/apps](https://zapier.com/apps).

<figure><img src="../../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

**How does Zapier x BitBadges work?**

Couple ways:

* Claims can be auto-completed from Zapier
* Post-success Zaps can implement utility or rewards
* Dynamic stores can be implemented with automatic updates of the list
* Claim infomration like codes can be automatically distributed to users

<figure><img src="../../../../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

When automating claims with Zapier, you will follow the approach of upon doing something (custom trigger), perform an action  (claim a token, distribute a code, add to dynamic store).

<figure><img src="../../../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

## **Error Handling**

You should also account for the fact that Zaps can partially execute. For example, if you have a Zap with 10 plugins and it fails on the 8th plugin, the Zap will not be considered a success but 8/10 plugins would be executed already. There is no rollback feature.

Similarly, if you are triggering a Zap during a claim's execution, there is no guarantee that the overall claim is successful because other plugins might fail.

## Triggers

The custom trigger step is left up to you to implement from any of the 7000+ app integrations.&#x20;

## **Actions**

With the claim or action step of the automated flow, you have a couple options depending on your use case.

{% content-ref url="../../automate-w-zapier/distribute-claim-information-tutorial/" %}
[distribute-claim-information-tutorial](../../automate-w-zapier/distribute-claim-information-tutorial/)
{% endcontent-ref %}

{% content-ref url="automatic-claim-tutorial.md" %}
[automatic-claim-tutorial.md](automatic-claim-tutorial.md)
{% endcontent-ref %}
