# 📚 Overview

This directory contains comprehensive developer documentation for the BitBadges blockchain's `x/managersplitter` module.

The Manager Splitter module provides a permissioned proxy system for managing token collections. It allows collection managers to delegate specific permissions to approved addresses while maintaining control over the collection.

For example, you may want to delegate an approved address to delete the collection on your behalf but none of the other permissions.

## Key Features

* **Permission Delegation**: Split collection management permissions among multiple addresses
* **Granular Control**: Define which addresses can perform which actions on a collection
* **Admin Override**: Maintain a permanent admin address with full control
* **Secure Execution**: Execute badge collection updates through the manager splitter with permission checks

## Architecture

The module creates **Manager Splitter** entities that:

1. Have a derived address that is to be set as the collection manager
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
