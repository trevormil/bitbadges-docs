# 📚 Overview

This directory contains comprehensive developer documentation for the BitBadges blockchain's `x/custom-ibc-hooks` module.

The Custom IBC Hooks module extends IBC transfer functionality by allowing users to execute custom actions (such as swaps and transfers) automatically when receiving IBC tokens. This enables complex cross-chain workflows in a single transaction.

## Key Features

-   **Swap and Transfer**: Automatically swap received tokens and transfer the output to a destination
-   **Swap and IBC Transfer**: Swap received tokens and send them to another chain via IBC
-   **Cross-chain DeFi**: Enable seamless cross-chain DeFi operations without manual intervention
-   **Atomic Execution**: All operations are executed atomically - either all succeed or all fail

## Architecture

The module operates as an IBC middleware hook that:

1. Intercepts incoming IBC transfer packets
2. Parses hook data from the transfer memo
3. Executes the IBC transfer first (to receive the tokens)
4. Executes the custom hook actions (swap, transfer, etc.)
5. Returns an error acknowledgement if the hook fails, rolling back the entire transaction

## Table of Contents

1. [Introduction](overview.md) - Overview and key concepts

## Quick Links

-   [BitBadges Chain Repository](https://github.com/bitbadges/bitbadgeschain)
-   [BitBadges Documentation](https://docs.bitbadges.io)

## Documentation Style

This documentation follows the [Cosmos SDK module documentation standards](https://docs.cosmos.network/main/building-modules/README) and is designed for developers building on or integrating with the BitBadges blockchain.
