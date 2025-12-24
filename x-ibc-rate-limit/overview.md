# Introduction

## Overview

The IBC Rate Limit module provides rate limiting functionality for Inter-Blockchain Communication (IBC) token transfers. It allows the chain to enforce limits on token transfers across IBC channels to prevent excessive inflows or outflows that could destabilize the chain's token supply.

## Purpose

The module serves as a security mechanism to:

* **Control supply shifts**: Limit the net change in token supply (inflows minus outflows) over specified timeframes
* **Limit unique senders**: Restrict the number of unique addresses that can send tokens through a channel within a timeframe
* **Enforce per-address limits**: Set limits on the number of transfers and total amount per address within a timeframe

## Key Concepts

### Rate Limit Configuration

Rate limits are configured per channel and denomination through the module's parameters. Each configuration can specify:

* **Channel ID**: The IBC channel to apply the limit to (empty string applies to all channels)
* **Denomination**: The token denomination to limit (must be specified)
* **Supply Shift Limits**: Limits on the absolute value of net flow (inflows - outflows)
* **Unique Sender Limits**: Limits on the number of unique sender addresses
* **Address Limits**: Per-address limits on transfer count and total amount

### Timeframes

The module supports three types of timeframes:

* **BLOCK**: Duration measured in blocks
* **HOUR**: Duration measured in hours (converted to blocks using block time)
* **DAY**: Duration measured in days (converted to blocks using block time)

Multiple timeframe limits can be configured for the same channel/denom combination, and all limits are checked. If any limit would be exceeded, the transfer is rejected.

## Authority

The module's parameters can be updated by the authority address, which defaults to the governance module account. This allows governance to adjust rate limits as needed.
