# Leveraging AI

A great way to implement both criteria and utility is through AI models and agents. This will follow along with the plugins / webhooks that we already have in place!

**Reward Side**

On the reward side, simply set up a post-success claim, receive any user identifiers or inputs needed, and trigger your actions. This can be custom webhooks to any model or service of your choice (Pipedream Connect / MCP, Zapier MCP, anything!), and we also have an in-site plugin for you to use the Zapier AI Actions / MCP service!

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

**Criteria Side**

On the criteria side, this is recommended to follow the dynamic stores approach. While you can check and trigger AI models during execution time, we typically recommend against this due to long wait times and poor UX. If you pre-add a user to a dynamic store, the claim process is good UX for the user.

{% content-ref url="dynamic-stores/" %}
[dynamic-stores](dynamic-stores/)
{% endcontent-ref %}

This is left open-ended. You could leverage Zapier AI Actions / MCP and the BitBadges integration to do this automatically.

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

```typescript
import fetch from 'node-fetch';

const ZAPIER_API_KEY = 'ADD_YOURS';

const options = { method: 'GET', headers: { 'x-api-key': ZAPIER_API_KEY } };

const fetchActions = async () => {
  const response = await fetch('https://actions.zapier.com/api/v2/ai-actions/', options);
  const data = await response.json();
  return data;
};

const executeAction = async (actionId: string, email: string) => {
  const options = {
    method: 'POST',
    headers: { 'x-api-key': ZAPIER_API_KEY, 'Content-Type': 'application/json' },
    body: `{"instructions":"Add ${email} to the store" ,"params":{}}`
  };

  await fetch(`https://actions.zapier.com/api/v2/ai-actions/${actionId}/execute/`, options)
    .then((response) => response.json())
    .then((response) => console.log(response));
};
```
