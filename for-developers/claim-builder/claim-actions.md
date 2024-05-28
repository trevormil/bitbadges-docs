# Claim Actions

## Claim Actions

Claim actions are the final action executed upon a successful claim. BitBadges supports a couple different types of claims depending on the use case.&#x20;

**On-Chain Balances**

Badges with the on-chain balance type are slightly different from the rest because it involves blockchain transactions to actually transfer the badges. For these claims, a successful claim will result in **reserving** the right to claim, but an additional transaction signature from the claimee is required to complete the process and obtain the badges. The transaction can be facilitated through the BitBadges site.

Behind the scenes, this works by issuing unique claim codes which are automatically populated when claming in-site. On-chian, these are the leaves of [Merkle challenge](../core-concepts/approval-criteria/merkle-challenges.md) in the approval criteria and are one-time use only to prevent replay attacks. These are not the same codes as the Codes plugin, if enabled.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Off-Chain Badges - Indexed**

For badges with the "Off-Chain - Indexed" balance type, everything works the same, except that upon successful claim, we actually assign the badges (aka assigning them off-chain). There is no reserve step or signature step for the user.  To work, BitBadges needs to store and host the balances (you can migrate to self-host after the claim is done).

**Address Lists**

For address list claims. If you can meet the criteria, you can get your address auto appended to the list.

**Custom Actions**

While we do not support custom generic claims out of the box, you can get creative and use one of the above to implement your own claims with custom actions. For example, create an address list claim and track successful claims / addresses but perform your own custom actions when you need to.

## **Reusing Plugins for Assigning Non-Indexed Balances**

Badges with "Off-Chain - Non-Indexed" balances type are slightly different because there is no claiming process because non-indexed balances are stateless. Thus, claims (which have state) are not possible.

For these, we reuse some of the plugins in order to query specific criteria and assign balances based on whether it is met or not. Because of the stateless requirement though, many options are disabled. The queries are executed on-demand whenever a user requests the balances for a specific address. If the criteria is met, we assign a balance of x1. If the criteria is not met, we assign a balance of x0 for all badges.&#x20;

Plugins that require anything other than just the user's crypto address are incompatible.&#x20;

<figure><img src="../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

