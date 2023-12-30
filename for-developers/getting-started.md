# 🚴♂ Getting Started

Where you start depends on your use case. At a minimum, we recommend familiarizing yourself with the core concepts before doing anything else.&#x20;

Don't forget to check the [tutorials ](tutorials.md)or ecosystem tools to see if someone else has already done what you are looking for!

Finished? Is it a tool that others can use? Let us know, so we can add it to [Ecosystem](../overview/ecosystem.md).

**Have questions or feedback?** Feel free to ask the BitBadges team or other developers in the BitBadges Discord.

## **Do you need to query blockchain data?**

Gain familiarity with the [BitBadges API](bitbadges-api/api.md) and [SDK](bitbadges-sdk/).

Do you need additional data not offered by the BitBadges API? Run your own indexer and customize the data you store! If not, simply use the BitBadges API for fetching data.

## **Need to verify ownership?**

Querying badge ownership is simply querying for the current balances; however, verifying also involves proving a user owns an address too. If you need to verify ownership of badges (and not just query them), check out [here](../overview/how-it-works/verification.md).

## **Do you need to submit blockchain transactions?**

The Create tab and other features on the BitBadges web app are pretty thorough and have lots of customization options. **For almost all use cases, these should be sufficient, and you should not need to custom generate your own transaction.**&#x20;

However, if you do, you can either 1) use the [custom helper broadcast tool](create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md) on the BitBadges site or 2) [generate them programmatically through the SDK](create-and-broadcast-txs/).

## **Creating a collection?**

**Do you want to self-host off-chain balances?** Learn about the different [balance types](core-concepts/balance-types.md) and see the [tutorials](tutorials/create-and-host-off-chain-balances.md) for self-hosting.

**Do you need to collect user addresses?** Create an [address survey](tutorials/custom-address-surveys.md).

## **Building a frontend?**

Use the [BitBadges website](bitbadges-frontend.md) code or [Blockin Quickstart](https://github.com/Blockin-Labs/blockin-quickstart) code as a starting point and/or reference. Feel free to reuse any UI components.

## **Need additional functionality?**

Consider expanding with the BitBadges SDK, or if you need to extend the interface on-chain, you can do so with a [CosmWasm smart contract](tutorials/create-a-wasm-contract.md).&#x20;

Or, reach out to us to see what we can do.

## **Do you want to run a blockchain node?**

{% content-ref url="bitbadges-blockchain/" %}
[bitbadges-blockchain](bitbadges-blockchain/)
{% endcontent-ref %}

