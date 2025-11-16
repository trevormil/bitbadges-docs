# IBC Rate Limit Module

## Overview

The IBC Rate Limit module provides rate limiting functionality for Inter-Blockchain Communication (IBC) token transfers. It allows the chain to enforce limits on token transfers across IBC channels to prevent excessive inflows or outflows that could destabilize the chain's token supply.

## Purpose

The module serves as a security mechanism to:
- **Control supply shifts**: Limit the net change in token supply (inflows minus outflows) over specified timeframes
- **Limit unique senders**: Restrict the number of unique addresses that can send tokens through a channel within a timeframe
- **Enforce per-address limits**: Set limits on the number of transfers and total amount per address within a timeframe

## Architecture

The module operates as an IBC middleware that intercepts IBC transfer packets before they are processed. It uses hooks to:
1. Check rate limits before processing incoming packets (`OnRecvPacketOverride`)
2. Check rate limits before sending packets (`SendPacketOverride`)
3. Track transfer statistics after successful transfers

## Key Concepts

### Rate Limit Configuration

Rate limits are configured per channel and denomination through the module's parameters. Each configuration can specify:

- **Channel ID**: The IBC channel to apply the limit to (empty string applies to all channels)
- **Denomination**: The token denomination to limit (must be specified)
- **Supply Shift Limits**: Limits on the absolute value of net flow (inflows - outflows)
- **Unique Sender Limits**: Limits on the number of unique sender addresses
- **Address Limits**: Per-address limits on transfer count and total amount

### Timeframes

The module supports three types of timeframes:

- **BLOCK**: Duration measured in blocks
- **HOUR**: Duration measured in hours (converted to blocks using block time)
- **DAY**: Duration measured in days (converted to blocks using block time)

Multiple timeframe limits can be configured for the same channel/denom combination, and all limits are checked. If any limit would be exceeded, the transfer is rejected.

### Flow Tracking

The module tracks:
- **Net Flow**: The difference between inflows and outflows for each channel/denom/timeframe combination
- **Unique Senders**: The set of unique sender addresses for each channel/timeframe combination
- **Address Transfer Data**: Transfer count and total amount per address/channel/denom/timeframe combination

### Window Management

Time windows are automatically reset when they expire. The module uses a fixed window approach where:
- Windows expire when `currentHeight >= WindowStart + WindowDuration`
- When a window expires, the tracking data is reset to zero
- New windows start at the current block height

## Integration

The module integrates with the IBC stack through:
- **IBC Hooks**: Implements `OnRecvPacketOverrideHooks` and `SendPacketOverrideHooks` interfaces
- **Combined Hooks**: Works alongside custom IBC hooks, with rate limit checks taking precedence
- **ICS4 Wrapper**: Intercepts packet sends through the ICS4Wrapper interface

## Authority

The module's parameters can be updated by the authority address, which defaults to the governance module account. This allows governance to adjust rate limits as needed.

## State Management

The module stores:
- **Parameters**: Rate limit configurations
- **Channel Flow**: Net flow state for each channel/denom/timeframe
- **Flow Windows**: Time window state for flow tracking
- **Unique Senders**: Set of unique sender addresses per channel/timeframe
- **Address Transfer Data**: Per-address transfer statistics

## Error Handling

When a rate limit is exceeded:
- The IBC packet is rejected with an error acknowledgement
- The transfer is not processed
- No state changes are made
- The error is logged for monitoring

