# 🚴‍♂️ Getting Started

Where you start depends on your use case. At a minimum, we recommend familiarizing yourself with the core concepts before doing anything else.

**Existing Tools?** Don't forget to check the [tutorials ](tutorials/)or ecosystem tools to see if someone else has already done what you are looking for! The BitBadges website also tries to be as developer friendly as possible, so in most cases, you will not even need to do anything technical yourself.

**Finished?** Is it a tool that others can use? Let us know, so we can add it to [Ecosystem](../overview/ecosystem/).

**Have questions or feedback?** Feel free to ask the BitBadges team or other developers in the BitBadges Discord. We are always willing to help!

## Quickstart

#### [Demo Hosted Quickstart URL](https://sea-turtle-app-2lz87.ondigitalocean.app/)

Check out the [BitBadges quickstart repository](https://github.com/BitBadges/bitbadges-quickstart). This gets you started for multiple aspects of BitBadges development (authentication, signing transactions, self-hosting, integrations, API / SDK, etc). See [here](https://github.com/BitBadges/bitbadges-quickstart). It also provides a simple implementation of a hybrid dApp where users can sign in with Web2 username / passwords, and everything is mapped to addresses behind the scenes.

The quickstart repo will be ever-evolving. New branches may offer different flavors (Tailwind CSS vs other UI). The main branch will also continue adding integrations, functionality, and more! Feel free to contribute to enhance the developer experience.

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

**Other Quickstarters:**

* Auth.js / Next.js SIWBB Template: [https://github.com/BitBadges/bitbadges-authjs-example](https://github.com/BitBadges/bitbadges-authjs-example)

## **Creating badges / lists?**

**Is everything you need supported by the BitBadges web app?** If so, then create a collection using the Create tab on the BitBadges web app. We envision 95% of badges can be created through this form. If not, see how to generate and broadcast a MsgCreateCollection transaction.

**Do you want to self-host off-chain balances or metadata?** See utorials for self-hosting. Note that you can manually enter your self-hosted URL into the Create form. Learn more here about the different [balance types](core-concepts/balance-types.md).

## Distributing badges?

Customizing the process of claiming a badge can be done directly via the BitBadges site when creating your badge collection or address list. The criteria can also be maintained through the "update" forms.

By default, users can claim on the BitBadges site under the claim component. If you want to customize your claim further, see the link below. This may include auto-completion of claims for your users, extending the functionality, distributing the claim information, creating a reusable plugin, or integrating with Zapier which allows you to auto-complete claims upon triggers from ovr 6000+ apps.

{% content-ref url="../overview/claim-builder/" %}
[claim-builder](../overview/claim-builder/)
{% endcontent-ref %}

{% content-ref url="../overview/distribution-tools-integrations.md" %}
[distribution-tools-integrations.md](../overview/distribution-tools-integrations.md)
{% endcontent-ref %}

**Off-Chain Balances - Self-Hosting**

For off-chain balances, you can manually control the allocation of badges via your self-hosted server. You can allocate them however you would like. Since they are off-chain, you can also access non-blockchain data (web2) to further enhance the allocation logic. For example, you may want to dynamically update badges based on who has paid their subscription for the month.

## **Query data?**

Gain familiarity with the [BitBadges API](bitbadges-api/api.md) and [SDK](bitbadges-sdk/).

Do you need additional data not offered by the BitBadges API? Run your own indexer and customize the data you store! If not, simply use the BitBadges API for fetching data.

## **Authenticate with badges?**

Querying badge ownership is simply querying for the current balances which can be done through the web app or alternative methods; however, authenticating also involves proving a user owns an address too (typically through a cryptographic siganture). If you need to verify ownership of badges (and not just query them), check out Blockin and the quickstart repository.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

## **Custom integrations?**

**Want to integrate BitBadges into your application / tool?** Check out the API / SDK and all other documentation to see what all is possible with BitBadges.

**Want to integrate your tool into the BitBadges site natively?** See creating a custom claim plugin, or reach out to us for a native integration, if custom claim plugins are not sufficient.

## **Submit blockchain transactions?**

The Create tab and other features on the BitBadges web app are pretty thorough and have lots of customization options. **For almost all use cases, these should be sufficient, and you should not need to custom program your own transaction generation and broadcast.**

However, if you do, you can either 1) use the [custom helper broadcast tool](create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md) on the BitBadges site or 2) [generate them programmatically through the SDK](create-and-broadcast-txs/). The blockchain node's CLI also works but only supports signatures from Cosmos addresses (not any other chain). You can also redirect to a popup of the helper broadcast tool, and all signing logic will be outsourced to BitBadges. You can just await the transaction hash.

## **Building a frontend?**

Use the quickstart code as a starting point and/or reference. See demos [here](https://blockin-quickstart.vercel.app/) and [here](https://blockin-quickstart-5gxg.vercel.app/). Feel free to reach out for code for any of the components we use in the frontend.

## **Need additional functionality?**

Consider customizing further using the BitBadges SDK, or if you need to extend the badge interface on-chain, you can do so with a [CosmWasm smart contract](tutorials/create-a-wasm-contract.md). Or, reach out to us to see what we can do.

## **Run a blockchain node?**

See [Run a Node](bitbadges-blockchain/run-a-node/).
