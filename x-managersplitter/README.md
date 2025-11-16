# 📚 Overview

This directory contains comprehensive developer documentation for the BitBadges blockchain's `x/managersplitter` module.

The Manager Splitter module provides a permissioned proxy system for managing badge collections. It allows collection managers to delegate specific permissions to approved addresses while maintaining control over the collection.

## Key Features

- **Permission Delegation**: Split collection management permissions among multiple addresses
- **Granular Control**: Define which addresses can perform which actions on a collection
- **Admin Override**: Maintain a permanent admin address with full control
- **Secure Execution**: Execute badge collection updates through the manager splitter with permission checks

## Architecture

The module creates **Manager Splitter** entities that:
1. Have a module-derived address that acts as the collection manager
2. Define permissions for each collection management action
3. Allow approved addresses to execute actions on behalf of the manager splitter
4. Maintain an admin address with permanent full control

## Table of Contents

1. [Introduction](overview.md) - Overview and key concepts
2. [Messages](messages/) - Transaction messages and handlers

## Message Reference

### Core Operations

* [MsgCreateManagerSplitter](messages/msg-create-manager-splitter.md) - Create a new manager splitter
* [MsgUpdateManagerSplitter](messages/msg-update-manager-splitter.md) - Update manager splitter permissions
* [MsgDeleteManagerSplitter](messages/msg-delete-manager-splitter.md) - Delete a manager splitter
* [MsgExecuteUniversalUpdateCollection](messages/msg-execute-universal-update-collection.md) - Execute collection updates through manager splitter
* [MsgUpdateParams](messages/msg-update-params.md) - Update module parameters

## Query Reference

For all Manager Splitter queries, please refer to the [BitBadges LCD API](https://lcd.bitbadges.io/).

The LCD provides comprehensive query endpoints for:
* Manager splitter information
* Permission configurations
* Module parameters
* And more

All queries follow the standard Cosmos SDK query patterns and can be accessed via REST API or gRPC.

## Quick Links

* [BitBadges Chain Repository](https://github.com/bitbadges/bitbadgeschain)
* [BitBadges Documentation](https://docs.bitbadges.io)

## Documentation Style

This documentation follows the [Cosmos SDK module documentation standards](https://docs.cosmos.network/main/building-modules/README) and is designed for developers building on or integrating with the BitBadges blockchain.

