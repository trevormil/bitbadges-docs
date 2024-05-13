# Overview

The BitBadges Claim builder is designed to be a comprehensive tool within the site. It allows you to easily establish the requirements for claiming your badges or being added to your address lists. Our goal is to make it adaptable to your specific needs through custom implementations or integrations, such as Zapier. Out of the box, it offers plenty of built-in features without requiring any additional coding. The end goal is for all use cases to be supported by the BitBadges claim builder with no code required.

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

## **Directory**

See all plugins and helper tools via the directory.

{% embed url="https://bitbadges.io/claims/directory" %}

## Creating Claims

Currently, creation of claims can currently only be created and maintained through the BitBadges site. You will get the chance to create and update claims when creating / updating a badge collection or address list. Completing claims can be automated / custom (see below).

## Claim Types

Behind the scenes, BitBadges manages the current state of the claim and performs the verification checks through its API. If a claim is successful, the claim action is completed. Claim actions are different dependent on what the claim is for.

**On-Chain Badges**

Badges with the on-chain balance type are slightly different from the rest because it involves blockchain transactions to actually transfer the badges. For these claims, a successful claim will result in getting the unique claim code which can be used on-chain to claim. By getting the unique claim code, you effectively **reserve** the right to claim, but an additional transaction signature from the claimee is required to complete the process and obtain the badges. Thse claim codes are handled behind the scenes and automatically populated when clamining in-site.

The on-chain claim codes are the leaves of [Merkle challenge](../core-concepts/approval-criteria/merkle-challenges.md) in the approval criteria and are one-time use only to prevent replay attacks. Note these are not the same codes as the Codes plugin, if enabled.

**Off-Chain Badges - Indexed**

For badges with the "Off-Chain - Indexed" balance type, everything works the same, except that upon successful claim, we actually assign the badges (aka assigning them off-chain). There is no reserve step or signature step for the user. &#x20;

For such claims, BitBadges needs to store and host the balances (you can migrate to self-host after the claim is done).

**Off-Chain Badges - Non-Indexed**

For badges with the "Off-Chain - Non-Indexed", claims cannot have state. All we do is allow you to check specific criteria in a stateless manner. Because of the stateless requirement, many options are disabled.

This is executed on-demand whenever a user requests the balances for a specific address. If the criteria is met, we assign a balance of x1. If the criteria is not met, we assign a balance of x0 for all badges.&#x20;

**Address Lists**

Lastly, we have address list claims. If you can meet the criteria, you can get your address appended to the list.

## Plugins / Customizations

The BitBadges claim builder adopts a plugin-based approach to allow for maximum customization and integrations.

**Creating a Plugin**

{% content-ref url="../tutorials/using-the-claim-builder-api-plugin.md" %}
[using-the-claim-builder-api-plugin.md](../tutorials/using-the-claim-builder-api-plugin.md)
{% endcontent-ref %}

**Core Plugins**

Core plugins are maintained by BitBadges and can actually keep track of state (e.g. who has claimed? how many times? etc). These are all executed and maintained internally. Core plugins are used when race conditions are important to be prevented, such as not allowing the same code to be used twice.

**Custom / API Plugins**

We also support custom API plugins. A brief summary is that we make an HTTP call potentially passing information about the claiming user, such as their address, email, socials, or other stuff. The user can also enter a custom input which will be passe, if configured.

If we get a success status code (200), we say the criteria has passed. Otherwise, we say the criteria has not passed.&#x20;

For custom plugins, it is important to note that these must be stateless, or if you maintain state, you should not rely on a successful response meaning a successful claim (other plugins could fail).

## Automatic Claim Completion

If your claim is configured in a way that you can claim on your users' behalf, you can complete the claim for them via the BitBadges API if you know their address. This is also possible via Zapier which allows you to connect to over 6000+ apps and custom triggers (e.g. purchase on Shopify completed -> get a badge).

Note the different claim actions explained above. Completing an on-chain claim will reserve a claim whereas indexed balances off-chain and address lists will actually transfer the badges / append to the list.

The easiest way to configure your claim to claim on others' behalf is to simply set up a a claim password that only you know. Then, you use that password when calling the API.&#x20;

{% content-ref url="../automate-w-zapier/" %}
[automate-w-zapier](../automate-w-zapier/)
{% endcontent-ref %}

{% content-ref url="../bitbadges-api/tutorials/completing-claims.md" %}
[completing-claims.md](../bitbadges-api/tutorials/completing-claims.md)
{% endcontent-ref %}

## Distribution of Claim Information

If your users require secret information to be able to claim (e.g. unique claim codes), you can do this via any method you prefer (BitBadges in-site claim alerts, email, SMS, etc). You know your users best!

## Collecting Addresses vs Allowing User Select&#x20;

Implementations, such as making automatic claims, may be easier or even require you to know your users'  crypto addresses beforehand. This may require a pre-collection step. Check out the directory for all the distribution tools.

Alternatively, you can create a claim gated with other criteria (e.g. 1 use per GitHub user) and allow the user to specify their address at claim time. This will always require the user to initiate the claim though.

## Extending the Functionality

We aim to make this process easy to extend and customize using self-implementations. This can be through creating your own plugins. Or, you can self-host your own claim and connect it with a BitBadges claim behind the scenes. You can get creative with this connection process.

For example, on BitBadges side, you may generate a claim with unique claim codes. On your end, you implement your claim functionality and assign a claim code to users who successfully satisfy your claim criteria (or auto-complete the claim for them).
