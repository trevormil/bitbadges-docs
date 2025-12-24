# Overview

This directory contains comprehensive developer documentation for the BitBadges blockchain's `x/ibc-rate-limit` module.

The IBC Rate Limit module provides rate limiting functionality for Inter-Blockchain Communication (IBC) token transfers. It allows the chain to enforce limits on token transfers across IBC channels to prevent excessive inflows or outflows that could destabilize the chain's token supply.

## Key Features

* **Control Supply Shifts**: Limit the net change in token supply (inflows minus outflows) over specified timeframes
* **Limit Unique Senders**: Restrict the number of unique addresses that can send tokens through a channel within a timeframe
* **Enforce Per-Address Limits**: Set limits on the number of transfers and total amount per address within a timeframe
* **Multiple Timeframes**: Support for block-based, hour-based, and day-based rate limits

## Architecture

The module operates as an IBC middleware that intercepts IBC transfer packets before they are processed. It uses hooks to:

1. Check rate limits before processing incoming packets (`OnRecvPacketOverride`)
2. Check rate limits before sending packets (`SendPacketOverride`)
3. Track transfer statistics after successful transfers

## Table of Contents

1. [Introduction](overview.md) - Overview and key concepts
