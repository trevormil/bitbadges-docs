# ❓ How Do I Check...?

On the criteria side, you can check any criteria. This is all configured in your claim builder via plugins! Plenty of in-site plugins are available, but you can always bring your own logic. Explore the claim builder and the rest of this documentation for more information.

<figure><img src="../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>

## Checking BitBadges Services - Badge Ownership, Points, List Membership

Configure your claim with in-site plugins for each respective service. Check badge ownership with the Badge Requirements plugin. Check points with the Points checker plugin. And so on. These are all supported for on-demand claims too.

## Claim Codes / Passwords

Configure the claim to check for one-time use only claim codes or secret passwords the user must provide. This is a universal option that can be used for any use case. No need for a specific integration or identifying a user specifically. Simply hand out the claim codes and let them claim.

## Receiving Attestations

Configure a custom webhook or plugin to request user attestations stored in their account.

## Checking Payments

Use the Stripe Payment plugin to gate claims to those who have paid.

## Gating by Address, Username, or Email List

Configure the corresponding plugin to check if the claiming user is on the list. Lists can be static or dynamic (see dynamic stores below),

## Dynamic Stores

Dynamic stores are a powerful option for gating by address, username, or email. These are standalone stores managed by you, stored by us. You control who is in the store's list via the site, Zapier Zaps, or custom API calls. It is completely flexible!

You can then attach your store to your claim and check that in real-time.

## Check Your Own Criteria

If you want to custom check criteria, configure your own custom plugin (publishable and reusable) or a webhook where you can receive user inputs, attestations, social identifiers, and more! Or, you can also auto-complete claims on behalf of users with the API or Zapier.
