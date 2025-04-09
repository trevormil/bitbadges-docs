# Overview

Similar to Zapier, Pipedream allows you to build automation workflows. We recommend checking out what is possible via Zapier first. Everything there can be done with Pipedream as well. Pipedream may have less connected apps, but it is more extendible and developer friendly.

**Custom Plugins**

Because Pipedream allows more customization on your level (in addition to just automation workflows), you can use their SDK to implement custom plugins seamlessly. This streamlines the development process and helps you connect to 1000s of applications seamlessly!

{% content-ref url="build-custom-plugins.md" %}
[build-custom-plugins.md](build-custom-plugins.md)
{% endcontent-ref %}

**Automation Workflows**

Automation workflows with Pipedream and BitBadges allow you to auto-complete claims, auto-add users to dynamic stores, implement post-claim logic, and more

Get started by creating a workflow with the BitBadges configuration. Then, you can copy/paste the code provided from the actions / triggers in the subpages of this section to programmatically interact with the BitBadges API. We refer you to the Zapier flows documentation for the high-level concepts and designs.

{% embed url="https://pipedream.com/apps/bitbadges" %}

{% content-ref url="../auto-completing-claims/automate-w-zapier/" %}
[automate-w-zapier](../auto-completing-claims/automate-w-zapier/)
{% endcontent-ref %}

{% content-ref url="workflow-actions/" %}
[workflow-actions](workflow-actions/)
{% endcontent-ref %}

{% content-ref url="workflow-triggers/" %}
[workflow-triggers](workflow-triggers/)
{% endcontent-ref %}

For the inputs, there are two ways of configuring. You may nee dto slightly adapt the templates to fit your desired flow.

Props - You set it manually via the props

```typescript
export default defineComponent({
  props: {
    bitbadges: {
      type: "app",
      app: "bitbadges",
    },
    claimInfo: {
      type: "string",
      "label":  "Claim Information",
      optional: false, // Use optional: false instead of required: true
    },
...
```

Dynamic - Parse from the trigger / another step (steps.trigger.event)

```typescript
export default defineComponent({
  props: {
    bitbadges: {
      type: "app",
      app: "bitbadges",
    }
  },
  async run({steps, $}) {
    try {
      const response = await axios($, {
        method: 'POST',
        url: `https://api.bitbadges.io/api/v0/claims/status/${steps.trigger.event.claimAttemptId}`,
 ...
```

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

