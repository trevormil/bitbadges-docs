# 🚴‍♂️ Getting Started

**Have questions or feedback?** Feel free to ask the BitBadges team or other developers in the BitBadges Discord. We are always willing to help!

## Quickstart

#### [Demo Hosted Quickstart URL](https://bitbadges.io/quickstart) - https://bitbadges.io/quickstart

Check out the [BitBadges quickstart repository](https://github.com/BitBadges/bitbadges-quickstart). This gets you started for multiple aspects of BitBadges development (authentication, signing transactions, self-hosting, integrations, API / SDK, etc).&#x20;

The quickstart repo will be ever-evolving. New branches may offer different flavors (Tailwind CSS vs other UI). The main branch will also continue adding integrations, functionality, and more! Feel free to contribute to enhance the developer experience.

<figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

**Other Links:**

* Auth.js / Next.js Sign In with BitBadges Template: [https://github.com/BitBadges/bitbadges-authjs-example](https://github.com/BitBadges/bitbadges-authjs-example)
* Developer Portal: [https://bitbadges.io/developer](https://bitbadges.io/developer)

## **Creating badges / lists / attestations / protocols?**

**Is everything you need supported by the BitBadges web app?** If so, then create using the Create tab on the BitBadges web app. We envision 95% of use cases can be created through this form. The forms are developer friendly too to allow you to customize small parts of the process.

**Do you want to self-host off-chain balances or metadata?** See tutorials for self-hosting. Note that you can manually enter your self-hosted URL into the Create form. Learn more here about the different [balance types](core-concepts/balances-transfers/balance-types.md).

For off-chain balances, you can manually control the allocation of badges via your self-hosted server. You can allocate them however you would like. Since they are off-chain, you can also access non-blockchain data (web2) to further enhance the allocation logic. For example, you may want to dynamically update badges based on who has paid their subscription for the month.

## Integrations? Gate distribution? Create a custom plugin?

BitBadges claims are a part of many aspects of BitBadges development. They can be used to gate badge distribution, gate spots on an address list, and even gate authentication attempts. This can be created managed directly in-site via the developer portal or other respective creation flows.

Create sample test claims in the [developer portal](https://bitbadges.io/developer) (Claim Tester tab) to see all that is possible!

{% content-ref url="../overview/claim-builder/" %}
[claim-builder](../overview/claim-builder/)
{% endcontent-ref %}

## **Query data?**

Gain familiarity with the [BitBadges API](bitbadges-api/api.md) and [SDK](bitbadges-sdk/).  Do you need additional data not offered by the BitBadges API? Run your own indexer and customize the data you store! If not, simply use the BitBadges API for fetching data.

## **Authenticate via Sign In with BitBadges?**

Authenticate your users from any chain, potentially checking badge ownership,  attestations, integrating with any supported app, and more along the way.

{% content-ref url="authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](authenticating-with-bitbadges/)
{% endcontent-ref %}

## **Custom integrations?**

**Want to integrate BitBadges into your application / tool?** Check out the API / SDK and all other documentation to see what all is possible with BitBadges.

**Want to integrate your tool into the BitBadges site natively?** See creating a custom claim plugin, or reach out to us for a native integration, if custom claim plugins are not sufficient.

## **Submit blockchain transactions?**

The Create tab and other features on the BitBadges web app are pretty thorough and have lots of customization options. **For almost all use cases, these should be sufficient, and you should not need to custom program your own transaction generation and broadcast.**

However, if you do, you can either 1) use the [custom helper broadcast tool](create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md) on the BitBadges site or 2) [generate them programmatically through the SDK](create-and-broadcast-txs/). The blockchain node's CLI also works but only supports signatures from Cosmos addresses (not any other chain). You can also redirect to a popup of the helper broadcast tool, and all signing logic will be outsourced to BitBadges. You can just await the transaction hash.

## **Need additional functionality?**

Consider customizing further using the BitBadges SDK, or if you need to extend the badge interface on-chain, you can do so with a [CosmWasm smart contract](bitbadges-blockchain/create-a-wasm-contract.md). Or, reach out to us to see what we can do.

## **Run a blockchain node?**

See [Run a Node](bitbadges-blockchain/run-a-node/).
