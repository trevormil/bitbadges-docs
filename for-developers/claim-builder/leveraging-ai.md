# Leveraging AI

> **Building an AI agent or bot that interacts with BitBadges directly?** See the [AI Agents & Bots](../ai-agents/) section for signing, broadcasting, MCP tools, and bot examples.

A great way to implement both criteria and utility is through AI models and agents. This can be done through custom plugins created in the [developer portal](https://bitbadges.io/developer).

**Criteria Side**

On the criteria side, we recommend the dynamic stores approach. While you can check and trigger AI models during execution time, we typically recommend against this due to long wait times and poor UX. If you pre-add a user to a dynamic store, the claim process is good UX for the user.

{% content-ref url="dynamic-stores/" %}
[dynamic-stores](dynamic-stores/)
{% endcontent-ref %}

**Reward Side**

On the reward side, create a custom plugin in the developer portal. Your plugin's endpoint can trigger any AI model or service upon successful claim processing.
