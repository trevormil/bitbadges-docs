# Overview

BitBadges Claims are designed to be a comprehensive tool directly within the site or via the API for you to customize the experience for you and your users. It allows you to easily establish the requirements for claiming your badges or being added to your address lists.&#x20;

The end goal is for all use cases to be supported by the BitBadges Claims. Out of the box, it offers plenty of built-in features with no code required and directly claimable by users in the site.

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

Or, you can can extend it with custom functionality or integrations, such as Zapier.

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

## **Plugins**

The BitBadges claim builder adopts a plugin-based approach to allow for maximum customization and integrations. See all plugins and helper tools via the directory.&#x20;

{% embed url="https://bitbadges.io/claims/directory" %}

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Approaches



## Extending the Functionality

We aim to make this process easy to extend and customize using self-implementations. This can be through creating your own plugins.&#x20;

However, plugins may be too restrictive since we just check status codes success or not on the BitBadges end. You can also self-host and verify your own claim and connect it with a BitBadges claim behind the scenes. You can get creative with this connection process. For example, on BitBadges side, you may generate a claim with unique password. On your end, you implement your claim functionality and auto complete the claim with the passsword oonly known by you.

## Completing the Claim



### Automatic Claim Completion

If your claim is configured in a way that you can claim on your users' behalf, you can complete the claim for them via the BitBadges API if you know their address. This is also possible via Zapier which allows you to connect to over 6000+ apps and custom triggers (e.g. purchase on Shopify completed -> get a badge).

Note the different claim actions explained above. Completing an on-chain claim will reserve a claim whereas indexed balances off-chain and address lists will actually transfer the badges / append to the list.

The easiest way to configure your claim to claim on others' behalf is to simply set up a a claim password that only you know. Then, you use that password when calling the API.&#x20;

{% content-ref url="auto-completing-claims/automate-w-zapier/" %}
[automate-w-zapier](auto-completing-claims/automate-w-zapier/)
{% endcontent-ref %}

{% content-ref url="../bitbadges-api/tutorials/completing-claims.md" %}
[completing-claims.md](../bitbadges-api/tutorials/completing-claims.md)
{% endcontent-ref %}

### Distribution of Claim Information

If your users require secret information to be able to claim (e.g. unique claim codes), you can do this via any method you prefer (BitBadges in-site claim alerts, email, SMS, etc). You know your users best!

### Collecting Addresses vs Allowing User Select&#x20;

Implementations, such as making automatic claims, may be easier or even require you to know your users'  crypto addresses beforehand. This may require a pre-collection step. Check out the directory for all the distribution tools.

Alternatively, you can create a claim gated with other criteria (e.g. 1 use per GitHub user) and allow the user to specify their address at claim time. This will always require the user to initiate the claim though.

