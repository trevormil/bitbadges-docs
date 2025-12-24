# 📚 Overview

This section contains documentation for additional Cosmos SDK modules used in the BitBadges blockchain.

## Table of Contents

### x/gamm

The `x/gamm` module implements the Generalized Automated Market Maker (GAMM) functionality for the BitBadges blockchain.

* [📚 Overview](x-gamm/) - Module overview and key differences from Osmosis
* [📖 Introduction](x-gamm/introduction.md) - Detailed introduction to GAMM concepts
* [📨 Messages](x-gamm/messages/) - Transaction messages and handlers
  * [MsgCreateBalancerPool](x-gamm/messages/msg-create-balancer-pool.md) - Create new balancer pool
  * [MsgExitPool](x-gamm/messages/msg-exit-pool.md) - Exit pool and receive tokens
  * [MsgJoinPool](x-gamm/messages/msg-join-pool.md) - Join existing pool with liquidity
  * [MsgSwapExactAmountIn](x-gamm/messages/msg-swap-exact-amount-in.md) - Swap exact amount of tokens in
  * [MsgSwapExactAmountInWithIBCTransfer](x-gamm/messages/msg-swap-exact-amount-in-with-ibc-transfer.md) - Swap and transfer via IBC

### x/custom-ibc-hooks

The Custom IBC Hooks module extends IBC transfer functionality by allowing users to execute custom actions automatically when receiving IBC tokens.

* [📚 Overview](x-custom-ibc-hooks/) - Module overview and key features
* [📖 Introduction](x-custom-ibc-hooks/overview.md) - Architecture and usage details

### x/ibc-rate-limit

The IBC Rate Limit module provides rate limiting functionality for Inter-Blockchain Communication (IBC) token transfers.

* [📚 Overview](x-ibc-rate-limit/) - Module overview and key features
* [📖 Introduction](x-ibc-rate-limit/overview.md) - Rate limiting concepts and configuration

### x/managersplitter

The Manager Splitter module provides a permissioned proxy system for managing token collections.

* [📚 Overview](x-managersplitter/) - Module overview and key features
* [📖 Introduction](x-managersplitter/overview.md) - Permission system and use cases
* [📨 Messages](x-managersplitter/messages/) - Transaction messages and handlers
  * [MsgCreateManagerSplitter](x-managersplitter/messages/msg-create-manager-splitter.md) - Create a new manager splitter
  * [MsgDeleteManagerSplitter](x-managersplitter/messages/msg-delete-manager-splitter.md) - Delete a manager splitter
  * [MsgExecuteUniversalUpdateCollection](x-managersplitter/messages/msg-execute-universal-update-collection.md) - Execute collection updates through manager splitter
  * [MsgUpdateManagerSplitter](x-managersplitter/messages/msg-update-manager-splitter.md) - Update manager splitter permissions
