# 🚴♂ Getting Started

Where you start depends on your use case. At a minimum, we recommend familiarizing yourself with the core concepts before doing anything else. &#x20;

**Existing Tools?** Don't forget to check the [tutorials ](tutorials/)or ecosystem tools to see if someone else has already done what you are looking for! The BitBadges website also tries to be as developer friendly as possible, so in most cases, you will not even need to do anything technical yourself.

**Finished?** Is it a tool that others can use? Let us know, so we can add it to [Ecosystem](../overview/ecosystem/).

**Have questions or feedback?** Feel free to ask the BitBadges team or other developers in the BitBadges Discord. We are always willing to help!

**Quickstart Repo:** Check out the BitBadges quickstart repository. This gets you started for multiple aspects of BitBadges development (authentication, signing transactions, self-hosting, API / SDK, etc). See [here](https://github.com/BitBadges/bitbadges-quickstart). It also provides a simple implementation of a hybrid dApp where users can sign in with Web2 username / passwords, and everything is mapped to addresses behind the scenes.

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

## **Creating a collection?**

**Is everything you need supported by the BitBadges web app?** If so, then create a collection using the Create tab on the BitBadges web app. We envision 95% of badges can be created through this form. If not, see how to generate and broadcast a MsgCreateCollection transaction.

**Do you want to self-host off-chain balances?** See the [tutorials](tutorials/create-and-host-off-chain-balances.md) for self-hosting. Note that you can manually enter your self-hosted URL into the Create form. Learn more here about the different [balance types](core-concepts/balance-types.md).&#x20;

**Do you need to collect user addresses for whitelists before creating?** Create an address survey using the Survey Mode option when creating an address list. Want to implement custom logic whenever a user adds to the survey? See [here](tutorials/custom-address-surveys.md).

## **Query data?**

Gain familiarity with the [BitBadges API](bitbadges-api/api.md) and [SDK](bitbadges-sdk/).

Do you need additional data not offered by the BitBadges API? Run your own indexer and customize the data you store! If not, simply use the BitBadges API for fetching data.

## **Authenticate with badges?**

Querying badge ownership is simply querying for the current balances which can be done through the web app or alternative methods; however, authenticating also involves proving a user owns an address too (typically through a cryptographic siganture). If you need to verify ownership of badges (and not just query them), check out Blockin and the quickstart repository. Notably, you may find the [Sign In with BitBadges](https://app.gitbook.com/s/AwjdYgEsUkK9cCca5DiU/developer-docs/getting-started/sign-in-with-bitbadges) page helpful.

{% content-ref url="https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/" %}
[Blockin](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)
{% endcontent-ref %}

## Distributing badges?

**On-Chain Balances**

The BitBadges site provides the building blocks (unique codes, passwords, whitelists, etc) for badge distribution. Users will typically claim directly on the official site. You can combine these building blocks with custom tools to further enhance the experience. For example, maybe you set up a unique code claim but distribute the codes with the help of a tool / your own frontend.

**Off-Chain Balances**&#x20;

For off-chain balances, you control the allocation of badges via your self-hosted server. You can allocate them however you would like. Since they are off-chain, you can also access non-blockchain data (web2) to further enhance the allocation logic.

{% content-ref url="../overview/distribution-tools-integrations.md" %}
[distribution-tools-integrations.md](../overview/distribution-tools-integrations.md)
{% endcontent-ref %}

## **Submit blockchain transactions?**

The Create tab and other features on the BitBadges web app are pretty thorough and have lots of customization options. **For almost all use cases, these should be sufficient, and you should not need to custom program your own transaction generation and broadcast.**

However, if you do, you can either 1) use the [custom helper broadcast tool](create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md) on the BitBadges site or 2) [generate them programmatically through the SDK](create-and-broadcast-txs/). The blockchain node's CLI also works but only supports signatures from Cosmos addresses (not any other chain). You can also redirect to a popup of the helper broadcast tool, and all signing logic will be outsourced to BitBadges. You can just await the transaction hash.

## **Building a frontend?**

Use the quickstart code as a starting point and/or reference. See demos [here](https://blockin-quickstart.vercel.app/) and [here](https://blockin-quickstart-5gxg.vercel.app/). Feel free to reuse any UI components from the official frontend as well.

## **Need additional functionality?**

Consider customizing further using the BitBadges SDK, or if you need to extend the badge interface on-chain, you can do so with a [CosmWasm smart contract](tutorials/create-a-wasm-contract.md). Or, reach out to us to see what we can do.

## **Run a blockchain node?**

See [Run a Node](bitbadges-blockchain/run-a-node.md).
