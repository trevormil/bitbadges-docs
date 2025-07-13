# Events

This document describes the event system used by the badges module for tracking operations, state changes, and integration with external systems.

## Table of Contents

- [Event System Overview](#event-system-overview)
- [Standard Events](#standard-events)
- [Indexer Events](#indexer-events)
- [Event Attributes](#event-attributes)
- [Event Patterns](#event-patterns)
- [Integration Examples](#integration-examples)

## Event System Overview

The badges module implements a dual-event system that provides both standard Cosmos SDK events for blockchain monitoring and specialized indexer events for external application integration.

### Event Categories

1. **Standard Events**: Follow Cosmos SDK conventions for blockchain monitoring
2. **Indexer Events**: Optimized for external indexing and application integration
3. **IBC Events**: For cross-chain operation tracking

### Event Emission Pattern

```go
// Standard pattern for all message handlers
func (k msgServer) MessageHandler(goCtx context.Context, msg *types.Message) (*types.MessageResponse, error) {
    ctx := sdk.UnwrapSDKContext(goCtx)
    
    // Execute business logic
    result, err := k.HandleMessage(ctx, msg)
    if err != nil {
        return nil, err
    }
    
    // Emit standard event
    ctx.EventManager().EmitEvent(
        sdk.NewEvent(
            types.TypeMsgMessageName,
            sdk.NewAttribute(types.AttributeKeyCreator, msg.Creator),
            // ... other attributes
        ),
    )
    
    // Emit indexer event
    ctx.EventManager().EmitEvent(
        sdk.NewEvent(
            "message",
            sdk.NewAttribute("module", "badges"),
            sdk.NewAttribute("sender", msg.Creator),
            sdk.NewAttribute("msg_type", types.TypeMsgMessageName),
        ),
    )
    
    return &types.MessageResponse{}, nil
}
```

## Standard Events

Standard events follow Cosmos SDK conventions and are emitted for all message types.

### Collection Events

#### CreateCollection

```go
Event Type: "create_collection"
Attributes:
  - collection_id: string (collection ID)
  - creator: string (creator address)
  - balance_type: string (balance tracking type)
```

**Example:**
```json
{
  "type": "create_collection",
  "attributes": [
    {"key": "collection_id", "value": "1"},
    {"key": "creator", "value": "bb1abc123..."},
    {"key": "balance_type", "value": "Standard"}
  ]
}
```

#### UpdateCollection

```go
Event Type: "update_collection"
Attributes:
  - collection_id: string (collection ID)
  - creator: string (updater address)
  - updated_fields: string (comma-separated list of updated fields)
```

#### DeleteCollection

```go
Event Type: "delete_collection"  
Attributes:
  - collection_id: string (deleted collection ID)
  - creator: string (deleter address)
```

### Transfer Events

#### TransferBadges

```go
Event Type: "transfer_badges"
Attributes:
  - collection_id: string (collection ID)
  - creator: string (transfer initiator)
  - num_transfers: string (number of individual transfers)
  - total_amount: string (total badge amount transferred)
```

**Example:**
```json
{
  "type": "transfer_badges",
  "attributes": [
    {"key": "collection_id", "value": "1"},
    {"key": "creator", "value": "bb1abc123..."},
    {"key": "num_transfers", "value": "3"},
    {"key": "total_amount", "value": "150"}
  ]
}
```

### Approval Events

#### UpdateUserApprovals

```go
Event Type: "update_user_approvals"
Attributes:
  - collection_id: string (collection ID)
  - creator: string (user address)
  - updated_outgoing: string ("true"/"false")
  - updated_incoming: string ("true"/"false")
  - updated_auto_approve: string ("true"/"false")
```

### Address List Events

#### CreateAddressLists

```go
Event Type: "create_address_lists"
Attributes:
  - creator: string (creator address)
  - num_lists: string (number of lists created)
  - list_ids: string (comma-separated list IDs)
```

### Dynamic Store Events

#### CreateDynamicStore

```go
Event Type: "create_dynamic_store"
Attributes:
  - store_id: string (store ID)
  - creator: string (creator address)
  - default_value: string ("true"/"false")
```

#### UpdateDynamicStore

```go
Event Type: "update_dynamic_store"
Attributes:
  - store_id: string (store ID)
  - creator: string (updater address)
  - new_default_value: string ("true"/"false")
```

#### DeleteDynamicStore

```go
Event Type: "delete_dynamic_store"
Attributes:
  - store_id: string (store ID)
  - creator: string (deleter address)
```

#### SetDynamicStoreValue

```go
Event Type: "set_dynamic_store_value"
Attributes:
  - store_id: string (store ID)
  - creator: string (setter address)
  - target_address: string (address being set)
  - value: string ("true"/"false")
```

## Indexer Events

Indexer events provide structured data optimized for external application consumption and database indexing.

### Universal Message Event

All message types emit a standardized indexer event:

```go
Event Type: "message"
Attributes:
  - module: "badges"
  - sender: string (message sender)
  - msg_type: string (specific message type)
  - msg_content: string (JSON-encoded message content)
```

**Example:**
```json
{
  "type": "message",
  "attributes": [
    {"key": "module", "value": "badges"},
    {"key": "sender", "value": "bb1abc123..."},
    {"key": "msg_type", "value": "create_collection"},
    {"key": "msg_content", "value": "{\"creator\":\"bb1abc123...\",\"balancesType\":\"Standard\",...}"}
  ]
}
```

### State Change Events

For operations that modify significant state, additional indexer events are emitted:

#### Collection State Change

```go
Event Type: "collection_state_change"
Attributes:
  - collection_id: string
  - change_type: string ("created", "updated", "deleted")
  - block_height: string
  - block_time: string
  - tx_hash: string
```

#### Balance Update

```go
Event Type: "balance_update"
Attributes:
  - collection_id: string
  - address: string
  - change_type: string ("increase", "decrease", "set")
  - badge_ids: string (JSON array of affected badge ID ranges)
  - amounts: string (JSON array of amount changes)
```

#### Approval Usage

```go
Event Type: "approval_usage"
Attributes:
  - collection_id: string
  - approval_level: string ("collection", "outgoing", "incoming")
  - approval_id: string
  - usage_type: string ("amount", "transfer_count", "challenge")
  - amount_used: string
  - remaining_limit: string
```

## Event Attributes

### Standard Attribute Keys

Defined in `x/badges/types/events.go`:

```go
const (
    AttributeKeyCreator           = "creator"
    AttributeKeyCollectionId      = "collection_id"
    AttributeKeyStoreId          = "store_id"
    AttributeKeyAddress          = "address"
    AttributeKeyValue            = "value"
    AttributeKeyBalanceType      = "balance_type"
    AttributeKeyNumTransfers     = "num_transfers"
    AttributeKeyTotalAmount      = "total_amount"
    AttributeKeyUpdatedFields    = "updated_fields"
    AttributeKeyListIds          = "list_ids"
    AttributeKeyNumLists         = "num_lists"
    AttributeKeyDefaultValue     = "default_value"
    AttributeKeyTargetAddress    = "target_address"
)
```

### Attribute Value Formats

#### Addresses
- Format: BitBadges address format (`bb1...`)
- Special values: `"Mint"`, `"Total"` for reserved addresses

#### Numeric Values
- Format: String representation of unsigned integers
- Large numbers: String to prevent precision loss
- Ranges: JSON array format `[{"start":"1","end":"10"}]`

#### Boolean Values
- Format: `"true"` or `"false"` (string representation)

#### Complex Data
- Format: JSON-encoded strings for structured data
- Proper escaping for nested JSON content

## Event Patterns

### Event Ordering

Events are emitted in a specific order within transaction execution:

1. **Pre-execution events**: Parameter validation, authorization checks
2. **State change events**: Actual state modifications
3. **Post-execution events**: Summary information, indexer data

### Transaction Failure Handling

Events are only persisted if the entire transaction succeeds. Failed transactions do not emit any events, ensuring consistency between events and state changes.

### Event Filtering

Applications can filter events by:
- **Event type**: Focus on specific operations
- **Module**: Filter for badges module events only
- **Attributes**: Filter by specific attribute values
- **Block range**: Time-based filtering

### Event Aggregation

Common aggregation patterns:
- **Daily transfer volumes**: Aggregate transfer events by date
- **Collection activity**: Track creation and update frequency
- **User activity**: Monitor user interaction patterns
- **Approval usage**: Track approval system utilization

## Integration Examples

### External Indexer

```go
// Example event processor for external indexer
func ProcessBadgesEvent(event sdk.Event) error {
    switch event.Type {
    case "create_collection":
        return indexCollectionCreation(event)
    case "transfer_badges":
        return indexBadgeTransfer(event)
    case "update_user_approvals":
        return indexApprovalUpdate(event)
    default:
        return nil // Ignore unknown events
    }
}

func indexCollectionCreation(event sdk.Event) error {
    var collectionId, creator, balanceType string
    
    for _, attr := range event.Attributes {
        switch attr.Key {
        case "collection_id":
            collectionId = attr.Value
        case "creator":
            creator = attr.Value
        case "balance_type":
            balanceType = attr.Value
        }
    }
    
    // Store in external database
    return database.InsertCollection(collectionId, creator, balanceType)
}
```

### WebSocket Notifications

```javascript
// Example WebSocket client for real-time notifications
const ws = new WebSocket('ws://localhost:26657/websocket');

ws.onopen = function() {
    // Subscribe to badges module events
    ws.send(JSON.stringify({
        "method": "subscribe",
        "params": ["tm.event='Tx' AND message.module='badges'"],
        "id": 1
    }));
};

ws.onmessage = function(event) {
    const data = JSON.parse(event.data);
    if (data.result && data.result.events) {
        processBadgesEvents(data.result.events);
    }
};

function processBadgesEvents(events) {
    events.forEach(event => {
        if (event.type.includes('create_collection')) {
            notifyNewCollection(event.attributes);
        } else if (event.type.includes('transfer_badges')) {
            notifyBadgeTransfer(event.attributes);
        }
    });
}
```

### Analytics Dashboard

```sql
-- Example SQL queries for analytics dashboard using indexed events

-- Daily collection creation stats
SELECT DATE(block_time) as date, COUNT(*) as collections_created
FROM collection_events 
WHERE event_type = 'create_collection'
GROUP BY DATE(block_time)
ORDER BY date DESC;

-- Top collections by transfer volume
SELECT collection_id, SUM(CAST(total_amount AS INTEGER)) as total_volume
FROM transfer_events
WHERE event_type = 'transfer_badges'
GROUP BY collection_id
ORDER BY total_volume DESC
LIMIT 10;

-- User activity metrics
SELECT creator, COUNT(*) as total_operations
FROM badge_events
GROUP BY creator
ORDER BY total_operations DESC;
```

### Mobile App Integration

```swift
// Example iOS integration for mobile notifications
class BadgesEventMonitor {
    func startMonitoring() {
        let eventSource = EventSource(url: "https://api.bitbadges.io/events")
        
        eventSource.onMessage { event in
            if let data = event.data?.data(using: .utf8),
               let badgeEvent = try? JSONDecoder().decode(BadgeEvent.self, from: data) {
                self.handleBadgeEvent(badgeEvent)
            }
        }
    }
    
    func handleBadgeEvent(_ event: BadgeEvent) {
        switch event.type {
        case "create_collection":
            showNotification("New badge collection created!")
        case "transfer_badges":
            if event.attributes.contains(where: { $0.key == "creator" && $0.value == userAddress }) {
                showNotification("Your badge transfer completed!")
            }
        default:
            break
        }
    }
}
```

### Event-Driven Automation

```python
# Example Python automation using events
import asyncio
import websockets
import json

class BadgesAutomation:
    def __init__(self):
        self.handlers = {
            'create_collection': self.handle_new_collection,
            'transfer_badges': self.handle_transfer,
            'update_user_approvals': self.handle_approval_update
        }
    
    async def monitor_events(self):
        uri = "wss://rpc.bitbadges.io/websocket"
        async with websockets.connect(uri) as websocket:
            # Subscribe to badges events
            subscribe_msg = {
                "method": "subscribe",
                "params": ["tm.event='Tx' AND message.module='badges'"],
                "id": 1
            }
            await websocket.send(json.dumps(subscribe_msg))
            
            async for message in websocket:
                event_data = json.loads(message)
                await self.process_event(event_data)
    
    async def handle_new_collection(self, event):
        collection_id = self.get_attribute(event, 'collection_id')
        creator = self.get_attribute(event, 'creator')
        
        # Automated response: send welcome message to creator
        await self.send_welcome_message(creator, collection_id)
    
    async def handle_transfer(self, event):
        collection_id = self.get_attribute(event, 'collection_id')
        total_amount = self.get_attribute(event, 'total_amount')
        
        # Automated response: update analytics dashboard
        await self.update_transfer_analytics(collection_id, total_amount)
```

This comprehensive event system enables rich integration possibilities while maintaining performance and providing detailed operational visibility for the badges module.