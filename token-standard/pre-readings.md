# 📔 Pre-Readings

Before diving deep into the behind the scenes of our token standard, we encourage you to do two things.

1\) Explore the landing and explore page ([https://bitbadges.io](https://bitbadges.io\)/)) in our site to get an idea of what is possible! Also, go through the create flow to see what all is possible!

<figure><img src="../.gitbook/assets/image (231).png" alt=""><figcaption></figcaption></figure>

2\) For a full A-Z description of what BitBadges is and why we built it, read this:

{% content-ref url="../overview/what-is-bitbadges.md" %}
[what-is-bitbadges.md](../overview/what-is-bitbadges.md)
{% endcontent-ref %}

3\) How does BitBadges tranferability work from a high-level?

**Mint Address**

The Mint address has unlimited balances. Any 'Mint' creates a token out of thin air.

**Circulating Supply**

The circulating supply is not a static number but rather the total amount of tokens transferred from the Mint address. Set approvals accordingly and set managerial permissions accordingly to limit the updatability of the approvals.

**Collection Approvals**

Collection approvals (minting and post-minting) define the overall rules for the collection. All transfers must always obey a collection approval. This gives you overarching control about the transferability of the collection. Examples: freezability, non-transferable, revoking, etc.

**User-Level Approvals**

The sender / receiver can also set user-level approvals that must be satisfied by default to gate who can send on their behalf or send to them.

These can be skipped / forcefully overriden by the collection-level approvals (for implementing forceful functionality like freezing, etc).&#x20;

**What is checked on each transfer?**

Each transfer must have sufficient balances, satisfy a collection approval, and satisfy user-level approvals if not overridden.&#x20;
