# Introduction

## Overview

The Manager Splitter module provides a permissioned proxy system for managing token collections. It allows collection managers to delegate specific permissions to approved addresses while maintaining control over the collection.

### Manager Splitter Entity

A Manager Splitter is a permissioned proxy that:

* **Address**: A module-derived address (derived from module name + ID) that serves as the collection manager. This is to be set as the actual manager in x/badges.
* **Admin**: A permanent admin address with full control over the splitter and all permissions
* **Permissions**: A mapping of collection permission types to execution criteria

Then, any token collection updates must be handled through this module and through the proxied address.

### Permission System

The module mirrors the Badges module's `CollectionPermissions` structure but maps each permission to `PermissionCriteria`:

* **CanDeleteCollection**: Permission to delete the collection
* **CanArchiveCollection**: Permission to archive the collection
* **CanUpdateStandards**: Permission to update collection standards
* **CanUpdateCustomData**: Permission to update custom data
* **CanUpdateManager**: Permission to update the manager
* **CanUpdateCollectionMetadata**: Permission to update collection metadata
* **CanUpdateValidTokenIds**: Permission to update valid token IDs
* **CanUpdateTokenMetadata**: Permission to update token metadata
* **CanUpdateCollectionApprovals**: Permission to update collection approvals

### Permission Criteria

Each permission can specify:

* **Approved Addresses**: A whitelist of addresses that can execute this permission

If a permission has no criteria set, it is denied by default (except for the admin).

### Admin Privileges

The admin address:

* Has full control over the manager splitter
* Can always execute all permissions
* Can create, update, and delete the manager splitter
* Cannot be changed after creation

## Use Cases

1. **Multi-sig Management**: Multiple addresses can manage different aspects of a collection
2. **Role-based Access**: Different addresses can have different permission levels
3. **Delegated Management**: Delegate specific management tasks to trusted addresses
4. **Governance Integration**: Use manager splitters for governance-controlled collections

## Security Model

1. **Admin Control**: Only the admin can create, update, or delete the manager splitter
2. **Permission Checks**: All actions check permissions before execution
3. **Address Validation**: All addresses are validated before use
4. **Atomic Execution**: Permission checks and execution are atomic
5. **Immutable Admin**: The admin address cannot be changed after creation

## Limitations

1. **Single Admin**: Each manager splitter has one permanent admin
2. **Approved Addresses Only**: Currently supports whitelist-based permissions only
3. **UniversalUpdateCollection Only**: Currently only supports executing `UniversalUpdateCollection` messages
4. **No Permission Inheritance**: Permissions must be explicitly set for each action type

## Alternative

This is an alternative to standard single manager approach. You can also explore setting up managerial systems with:

* x/group - Cosmos SDK module for multisigs / DAOs and voting
* DAO tooling
* Custom WASM or EVM contracts
