# Table of contents

## Overview

* [👋 BitBadges Overview](README.md)
* [🎨 Use Cases](overview/use-cases.md)
* [🧠 Core Concepts](overview/how-it-works/README.md)
  * [Accounts](overview/how-it-works/accounts.md)
  * [Badges vs Lists](overview/how-it-works/badges-vs-address-lists.md)
  * [Verifiable Attestations](overview/how-it-works/verifiable-attestations.md)
  * [Distribution / Collection](overview/how-it-works/distribution.md)
  * [Manager](overview/how-it-works/manager.md)
  * [Total Supplys](overview/how-it-works/total-supplys.md)
  * [Metadata](overview/how-it-works/metadata.md)
  * [Time-Dependent Ownership](overview/how-it-works/time-dependent-ownership.md)
  * [Balances Types](overview/how-it-works/balances-types.md)
  * [Transferability](overview/how-it-works/transferability.md)
  * [Standards](overview/how-it-works/standards.md)
  * [Aliases](overview/how-it-works/aliases.md)
* [🪙 Launch Phases](overview/launch-phases.md)
* [🕵️ Authentication with Badges](overview/verification-tools.md)
* [🔀 Distribution](overview/distribution-tools-integrations.md)
* [📱 Mobile Support](overview/mobile-support.md)
* [🤝 Link Sharing](overview/link-sharing.md)
* [🤵‍♂️ LinkedIn Certifications](overview/linkedin-certifications.md)
* [🧩 Chrome Extension](overview/chrome-extension.md)
* [🦊 MetaMask Snap](overview/metamask-snap.md)
* [📊 Explorers](overview/explorers.md)
* [🌴 Ecosystem](overview/ecosystem/README.md)
  * [📽️ Protocols](overview/protocols/README.md)
    * [BitBadges Follow Protocol](overview/protocols/bitbadges-follow-protocol.md)
    * [Experiences Protocol](overview/protocols/experiences-protocol.md)
  * [Blockin](overview/ecosystem/blockin.md)
* [🙂 Team / Contact Us](overview/team-contact-us.md)
* [❓ FAQ](overview/faq.md)

## ⌨️ For Developers

* [🚴‍♂️ Getting Started](for-developers/getting-started.md)
* [🧠 Core Concepts](for-developers/core-concepts/README.md)
  * [👤 Accounts](for-developers/core-concepts/accounts.md)
  * [👥 Accounts (Low-Level)](for-developers/core-concepts/accounts-technical.md)
  * [🔢 Big Numbers](for-developers/core-concepts/big-numbers.md)
  * [🔢 Uint Ranges](for-developers/core-concepts/uint-ranges.md)
  * [📧 Address Lists](for-developers/core-concepts/address-lists-lists.md)
  * [⏳ Timelines](for-developers/core-concepts/timelines.md)
  * [🖼️ Metadata](for-developers/core-concepts/metadata.md)
  * [📊 Balances](for-developers/core-concepts/balances.md)
  * [🪙 Balance Types](for-developers/core-concepts/balance-types.md)
  * [➕ Creating Badges](for-developers/core-concepts/creating-badges.md)
  * [🖊️ Standards](for-developers/core-concepts/standards.md)
  * [🤝 Transferability / Approvals](for-developers/core-concepts/transferability-approvals.md)
  * [✅ Approval Criteria](for-developers/core-concepts/approval-criteria/README.md)
    * [Overview](for-developers/core-concepts/approval-criteria/overview.md)
    * [$BADGE Transfers](for-developers/core-concepts/approval-criteria/usdbadge-transfers.md)
    * [Overrides](for-developers/core-concepts/approval-criteria/overrides.md)
    * [Must Own Badges](for-developers/core-concepts/approval-criteria/must-own-badges.md)
    * [Approval Trackers](for-developers/core-concepts/approval-criteria/approval-trackers.md)
    * [Tallied Approval Amounts](for-developers/core-concepts/approval-criteria/tallied-approval-amounts.md)
    * [Max Number of Transfers](for-developers/core-concepts/approval-criteria/max-number-of-transfers.md)
    * [Predetermined Balances](for-developers/core-concepts/approval-criteria/predetermined-balances.md)
    * [Requires](for-developers/core-concepts/approval-criteria/requires.md)
    * [Merkle Challenges](for-developers/core-concepts/approval-criteria/merkle-challenges.md)
    * [ZK Proofs](for-developers/core-concepts/approval-criteria/zk-proofs.md)
    * [Extending the Approval (Advanced)](for-developers/core-concepts/approval-criteria/linking-trackers-advanced.md)
  * [🔐 Permissions](for-developers/core-concepts/permissions/README.md)
    * [Action Permission](for-developers/core-concepts/permissions/action-permission.md)
    * [Timed Update Permission](for-developers/core-concepts/permissions/timed-update-permission.md)
    * [Timed Update With Badge Ids Permission](for-developers/core-concepts/permissions/timed-update-with-badge-ids-permission.md)
    * [Balances Action Permission](for-developers/core-concepts/permissions/balances-action-permission.md)
    * [Update Approval Permission](for-developers/core-concepts/permissions/update-approval-permission.md)
  * [🕒 Different Time Fields](for-developers/core-concepts/different-time-fields.md)
  * [🔓 Archived Collections](for-developers/core-concepts/archived-collections.md)
  * [✍️ Custom Data](for-developers/core-concepts/custom-data.md)
  * [⚓ Anchors](for-developers/core-concepts/anchors.md)
  * [🗺️ Maps](for-developers/core-concepts/maps.md)
  * [🤖 Protocols](for-developers/core-concepts/protocols.md)
  * [🕵️ ZK / Privacy Preservation](for-developers/core-concepts/zk-privacy-preservation.md)
  * [🗝️ Verifiable Attestations](for-developers/core-concepts/verifiable-attestations/README.md)
    * [Creating a Attestation](for-developers/core-concepts/verifiable-attestations/creating-a-attestation.md)
    * [Creating and Verifying a Proof](for-developers/core-concepts/verifiable-attestations/creating-and-verifying-a-proof.md)
    * [Presentations](for-developers/core-concepts/verifiable-attestations/presentations.md)
* [📚 BitBadges API](for-developers/bitbadges-api/README.md)
  * [API](for-developers/bitbadges-api/api.md)
  * [Routes](https://bitbadges.stoplight.io/docs/bitbadges)
  * [Typed SDK Types](https://bitbadges.github.io/bitbadgesjs/packages/bitbadgesjs-sdk/docs/classes/BitBadgesAPI.html)
  * [Tutorials](for-developers/bitbadges-api/tutorials/README.md)
    * [Fetching Collections](for-developers/bitbadges-api/tutorials/fetching-collections.md)
    * [Fetching Accounts](for-developers/bitbadges-api/tutorials/fetching-accounts.md)
    * [Fetching Balances](for-developers/bitbadges-api/tutorials/fetching-balances.md)
    * [Fetching Lists](for-developers/bitbadges-api/tutorials/fetching-lists.md)
    * [Getting Claims](for-developers/bitbadges-api/tutorials/getting-claims.md)
    * [Completing Claims](for-developers/bitbadges-api/tutorials/completing-claims.md)
    * [Managing Claims](for-developers/bitbadges-api/tutorials/managing-claims.md)
  * [Concepts](for-developers/bitbadges-api/concepts/README.md)
    * [Native Chain Algorithm](for-developers/bitbadges-api/concepts/native-chain-algorithm.md)
    * [Refresh / Claim Completion Queue](for-developers/bitbadges-api/concepts/refresh-queue.md)
    * [Designing for Compatibility](for-developers/bitbadges-api/concepts/designing-for-compatibility.md)
  * [Limits / Restrictions](for-developers/bitbadges-api/limits-restrictions.md)
* [⚒️ BitBadges JS / SDK](for-developers/bitbadges-sdk/README.md)
  * [Overview](for-developers/bitbadges-sdk/overview.md)
  * [SDK Types](for-developers/bitbadges-sdk/sdk-types.md)
  * [Full Documentation](for-developers/bitbadges-sdk/full-documentation.md)
  * [Common Snippets](for-developers/bitbadges-sdk/common-snippets/README.md)
    * [Address Conversions](for-developers/bitbadges-sdk/common-snippets/address-conversions.md)
    * [NumberType Conversions](for-developers/bitbadges-sdk/common-snippets/numbertype-conversions.md)
    * [Uint Ranges](for-developers/bitbadges-sdk/common-snippets/uint-ranges.md)
    * [Creating, Signing, and Broadcasting Txs](for-developers/bitbadges-sdk/common-snippets/creating-signing-and-broadcasting-txs.md)
    * [Balances](for-developers/bitbadges-sdk/common-snippets/balances.md)
    * [Transfers](for-developers/bitbadges-sdk/common-snippets/transfers-w-increments.md)
    * [Address Lists](for-developers/bitbadges-sdk/common-snippets/address-lists.md)
    * [Badge Metadata](for-developers/bitbadges-sdk/common-snippets/badge-metadata.md)
    * [Approvals / Transferability](for-developers/bitbadges-sdk/common-snippets/get-unhandled-approvals.md)
    * [Off-Chain Balances](for-developers/bitbadges-sdk/common-snippets/off-chain-balances.md)
    * [Timelines](for-developers/bitbadges-sdk/common-snippets/timelines.md)
    * [Aliases](for-developers/bitbadges-sdk/common-snippets/aliases.md)
* [🏗️ BitBadges Claims](overview/claim-builder/README.md)
  * [Overview](for-developers/claim-builder/overview.md)
  * [Claim Actions](for-developers/claim-builder/claim-actions.md)
  * [Plugin ID vs Instance ID](for-developers/claim-builder/plugin-id-vs-instance-id.md)
  * [Creating / Maintaining Claims](for-developers/claim-builder/creating-maintaining-claims.md)
  * [Plugins](for-developers/claim-builder/plugins/README.md)
    * [Plugins Directory](https://bitbadges.io/claims/directory)
    * [Creating a Custom Plugin](for-developers/claim-builder/plugins/creating-a-custom-plugin.md)
    * [BitBadges Created Plugins](for-developers/claim-builder/plugins/bitbadges-created-plugins/README.md)
      * [Number of Uses](for-developers/claim-builder/plugins/bitbadges-created-plugins/number-of-uses.md)
      * [Signed in to BitBadges](for-developers/claim-builder/plugins/bitbadges-created-plugins/signed-in-to-bitbadges.md)
      * [Socials / Email Plugins](for-developers/claim-builder/plugins/bitbadges-created-plugins/authentication-based-plugins.md)
      * [Codes](for-developers/claim-builder/plugins/bitbadges-created-plugins/codes.md)
      * [Password](for-developers/claim-builder/plugins/bitbadges-created-plugins/password.md)
      * [Transfer Times](for-developers/claim-builder/plugins/bitbadges-created-plugins/transfer-times.md)
      * [Whitelist](for-developers/claim-builder/plugins/bitbadges-created-plugins/whitelist.md)
      * [Halt](for-developers/claim-builder/plugins/bitbadges-created-plugins/halt.md)
      * [IP Restrictions](for-developers/claim-builder/plugins/bitbadges-created-plugins/ip-restrictions.md)
      * [Geolocation](for-developers/claim-builder/plugins/bitbadges-created-plugins/geolocation.md)
      * [Discord Server](for-developers/claim-builder/plugins/bitbadges-created-plugins/discord-server.md)
      * [Twitch Follow / Subscription](for-developers/claim-builder/plugins/bitbadges-created-plugins/twitch-follow-subscription.md)
      * [GitHub Contributions](for-developers/claim-builder/plugins/bitbadges-created-plugins/github-contributions.md)
      * [Min $BADGE](for-developers/claim-builder/plugins/bitbadges-created-plugins/min-usdbadge.md)
      * [Ownership Requirements](for-developers/claim-builder/plugins/bitbadges-created-plugins/ownership-requirements.md)
  * [Universal Approaches](for-developers/claim-builder/universal-approaches.md)
  * [Integrate with Zapier](for-developers/claim-builder/auto-completing-claims/automate-w-zapier/README.md)
    * [Overview](for-developers/claim-builder/auto-completing-claims/automate-w-zapier/overview.md)
    * [Distribute Claim Information Tutorial](for-developers/claim-builder/auto-completing-claims/automate-w-zapier/distribute-claim-information-tutorial.md)
    * [Automatic Claim Tutorial](for-developers/claim-builder/auto-completing-claims/automate-w-zapier/automatic-claim-tutorial.md)
  * [Auto-Complete Claims w/ BitBadges API](for-developers/claim-builder/auto-completing-claims/auto-complete-claims-w-bitbadges-api.md)
  * [Other Tutorials](for-developers/claim-builder/other-tutorials/README.md)
    * [Create a Tutorial](for-developers/claim-builder/other-tutorials/create-a-tutorial.md)
    * [Get Integration User IDs](for-developers/claim-builder/other-tutorials/get-integration-user-ids/README.md)
      * [Get Discord User ID](for-developers/claim-builder/other-tutorials/get-integration-user-ids/get-discord-user-id.md)
      * [Get Discord Server ID](for-developers/claim-builder/other-tutorials/get-integration-user-ids/discord.md)
      * [X / Twitch / GitHub IDs](for-developers/claim-builder/other-tutorials/get-integration-user-ids/x-twitch-github-ids.md)
    * [Build a Distribution Tool](for-developers/claim-builder/other-tutorials/build-a-distribution-tool.md)
    * [Calendly - Get Emails](for-developers/claim-builder/other-tutorials/calendly-get-emails.md)
    * [Google Calendar - Get Emails](for-developers/claim-builder/other-tutorials/google-calendar-get-emails.md)
    * [Zoom - Get Emails](for-developers/claim-builder/other-tutorials/zoom-get-emails.md)
    * [Eventbrite - Get Emails](for-developers/claim-builder/other-tutorials/eventbrite-get-emails.md)
    * [Cvent - Get Emails](for-developers/claim-builder/other-tutorials/cvent-get-emails.md)
    * [Mailchimp - Get Emails](https://mailchimp.com/help/view-export-contacts/)
* [💾 Self-Hosted Balances](for-developers/self-hosted-balances.md)
  * [Overview](for-developers/self-hosted-balances/overview.md)
  * [Examples / Tutorials](for-developers/self-hosted-balances/examples-tutorials/README.md)
    * [Indexed](for-developers/self-hosted-balances/examples-tutorials/indexed.md)
    * [ETH First Tx - (Non-Indexed)](https://github.com/BitBadges/bitbadges-indexer/blob/master/src/routes/ethFirstTx.ts)
* [🖱️ Sign In with BitBadges](for-developers/authenticating-with-bitbadges/README.md)
  * [Overview](for-developers/sign-in-with-bitbadges/overview.md)
  * [Framework Templates](for-developers/authenticating-with-bitbadges/framework-templates/README.md)
    * [Auth.js](for-developers/authenticating-with-bitbadges/framework-templates/auth.js.md)
    * [Auth0](for-developers/authenticating-with-bitbadges/framework-templates/auth0.md)
    * [PassportJS](for-developers/authenticating-with-bitbadges/framework-templates/passportjs.md)
  * [Authentication URL + Parameters](for-developers/sign-in-with-bitbadges/authentication-url-+-parameters.md)
  * [Setting Up an App](for-developers/sign-in-with-bitbadges/setting-up-an-app.md)
  * [Ownership Requirements](for-developers/sign-in-with-bitbadges/challenge-parameters.md)
  * [Generating the URL](for-developers/sign-in-with-bitbadges/generating-the-url.md)
  * [Approaches](for-developers/authenticating-with-bitbadges/approaches/README.md)
    * [Manual](for-developers/authenticating-with-bitbadges/approaches/manual.md)
    * [Redirected Callback](for-developers/sign-in-with-bitbadges/approaches/redirected-callback.md)
  * [Verification](for-developers/authenticating-with-bitbadges/verification/README.md)
  * [Add-Ons](for-developers/sign-in-with-bitbadges/add-ons/README.md)
    * [Extending with Zapier](for-developers/authenticating-with-bitbadges/verification/spreadsheet-session-management.md)
    * [Badge-Gating Discord Servers](for-developers/authenticating-with-bitbadges/verification/badge-gating-discord-servers.md)
  * [Blockin Docs](https://blockin.gitbook.io/blockin)
* [🔃 Create, Generate, and Sign Txs](for-developers/create-and-broadcast-txs/README.md)
  * [Cosmos SDK Msgs](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/README.md)
    * [MsgCreateCollection](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgcreatecollection.md)
    * [MsgUpdateCollection](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgupdatecollection.md)
    * [MsgDeleteCollection](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgdeletecollection.md)
    * [MsgCreateAddressLists](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgcreateaddresslists.md)
    * [MsgTransferBadges](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgtransferbadges.md)
    * [MsgUpdateUserApprovals](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgupdateuserapprovals.md)
    * [MsgUniversalUpdateCollection](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msguniversalupdatecollection.md)
    * [MsgCreateMap](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgcreateprotocol.md)
    * [MsgUpdateMap](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgupdateprotocol.md)
    * [MsgDeleteMap](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgdeleteprotocol.md)
    * [MsgSetValue](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgsetcollectionforprotocol.md)
    * [MsgStoreCodeCompat](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgstorecodecompat.md)
    * [MsgInstantiateContractCompat](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msginstantiatecontractcompat.md)
    * [MsgExecuteContractCompat](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgexecutecontractcompat.md)
    * [MsgSend](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgsend.md)
    * [MsgAddCustomData](for-developers/create-and-broadcast-txs/cosmos-sdk-msgs/msgaddcustomdata.md)
  * [Msg Examples](for-developers/create-and-broadcast-txs/msg-examples.md)
  * [Transaction Context](for-developers/create-and-broadcast-txs/transaction-context.md)
  * [Generate Msg Contents](for-developers/create-and-broadcast-txs/generate-msg-contents.md)
  * [Signing - Cosmos](for-developers/create-and-broadcast-txs/signing-cosmos.md)
  * [Signing - Ethereum](for-developers/create-and-broadcast-txs/signing-ethereum.md)
  * [Signing - Solana](for-developers/create-and-broadcast-txs/signing-solana.md)
  * [Signing - Bitcoin](for-developers/create-and-broadcast-txs/signing-bitcoin.md)
  * [Broadcast to a Node](for-developers/create-and-broadcast-txs/broadcast-to-a-node.md)
  * [Sign + Broadcast - bitbadges.io](for-developers/create-and-broadcast-txs/sign-+-broadcast-bitbadges.io.md)
* [💰 Monetizing Badges](for-developers/monetizing-apps-badges.md)
* [🧑‍🏫 Other Tutorials](for-developers/tutorials/README.md)
  * [Create a Smart Contract](for-developers/tutorials/create-a-wasm-contract.md)
* [💻 BitBadges Frontend](for-developers/bitbadges-frontend.md)
* [⛓️ BitBadges Blockchain](for-developers/bitbadges-blockchain/README.md)
  * [Overview](for-developers/bitbadges-blockchain/overview.md)
  * [REST API Docs - Node](for-developers/bitbadges-blockchain/rest-api-docs-node.md)
  * [Staking / Validators](overview/staking-usdbadge.md)
  * [Run a Node](for-developers/bitbadges-blockchain/run-a-node/README.md)
    * [Run a Mainnet Node](for-developers/bitbadges-blockchain/run-a-node/run-a-mainnet-node.md)
    * [Run a Local Dev Node](for-developers/bitbadges-blockchain/run-a-node/run-a-local-dev-node.md)
* [🤝 Protocols](for-developers/protocols/README.md)
  * [BitBadges Follow Protocol](for-developers/protocols/bitbadges-follow-protocol.md)
  * [Experiences Protocol](for-developers/protocols/experiences-protocol.md)
* [✏️ Chain Details](for-developers/chain-details.md)
* [👨‍💻 Contributing](for-developers/contributing.md)
* [❓ FAQ - Dev](for-developers/faq-dev.md)
