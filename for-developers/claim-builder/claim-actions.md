# Claim Types

## Claim Actions

Claim actions are the final action executed upon a successful claim. BitBadges supports a couple different types of claims depending on the use case.&#x20;

**On-Chain Balances**

Badges with the on-chain balance type are slightly different from the rest because the actual transfer involves blockchain transactions.

**For these claims, a successful claim will result in reserving the right to claim, not actually completing the claim.** Behind the scenes, we reserve the unique claim code for the user. The on-chain transaction will eventually specify that claim code in the transfer transaction.

On-chain, these are the leaves of [Merkle challenge](../core-concepts/balances-transfers/approval-criteria/merkle-challenges.md) in the approval criteria and are one-time use only to prevent replay attacks. These are not the same codes as the Codes plugin, if enabled.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Off-Chain Badges - Indexed**

For badges with the "Off-Chain - Indexed" balance type, everything works the same, except that upon successful claim, we actually allocate the badges (aka assigning them off-chain). There is no reserve step or transaction step for the user other than the claiming step. To work, BitBadges needs to store and host the balances (you can migrate to self-host after the claim is done).

**Address Lists**

For address list claims. If you can meet the criteria, you can get your address auto appended to the list.

**Generic Claims**

We also support generic claims with no specific action. These are typically used in combination with SIWBB.

**Custom Actions**

You can also implement the actions on your end by checking claim attempt statuses, receiving success webhooks, custom plugins, etc.

## Custom Claim Numbers

By default, we use an incrementing claim number system. For example, claim #1, then claim #2, etc. However, certain plugins may require specifying claim numbers in order to function. Claim numbers can be used to distribute specific badges. They are not applicable to address lists or non-indexed (see below) actions.

Only one plugin is allowed to assign claim numbers which is determined by the **assignMethod** of the claim. If the assignMethod === a plugin's unique instance ID, we allow it to assign claim numbers.

## Non-Indexed Claims (On-Demand)

**Non-indexed or on-demand claims are a special type of claim that has unique properties. Notably, they are autonomous, self-contained, can be fetched on-demand, stateless, does not require any user inputs, sessions, and can function with just a user address / creator parameters.**&#x20;

In other words, nothing is stored, there is no inputs or claim step, and these can be queried / fetched dynamically from source (on-demand) at any time. For the claim to have these properties, all plugins must have the properties.&#x20;

Some example plugins include:

-   Public badge ownership queries
-   Minimum $BADGE balance checks
-   Transfer time checks

For example, checking a minimum balance of $BADGE is safe to use because we always know a user's balance at any given time wihtout user interaction and just their address.

Because of these unique properties, you are able to do the following, for example:

-   SIWBB requests would not require the "claim step" from the user. We can just check all such information on the go.
-   Non-indexed (on-demand) balances meaning that we just lookup balances for any user at any given time. No transfers need to take place. (see picture below).
