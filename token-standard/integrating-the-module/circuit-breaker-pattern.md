# Circuit Breaker (Replacing x/circuit)

The Cosmos SDK `x/circuit` module is deprecated. You can replace it with a stateless ante handler decorator that checks token ownership via `x/tokenization`. The entire circuit breaker is about 20 lines of Go. All state lives in token ownership -- no new store, no migration.

## How It Works

1. Create a "halt token" collection on-chain. Each token ID maps to a message type you want to be able to disable (e.g., token ID 1 = `MsgSend`, token ID 2 = `MsgTransfer`).
2. When the authority address holds a halt-token for a given message type, that message type is rejected by the ante handler.
3. Burn the halt-token (or let it expire) to re-enable the message type.

## Ante Handler Decorator

```go
package app

import (
	sdkmath "cosmossdk.io/math"
	sdk "github.com/cosmos/cosmos-sdk/types"
	tokenizationkeeper "github.com/bitbadges/bitbadgeschain/x/tokenization/keeper"
	tokenizationtypes "github.com/bitbadges/bitbadgeschain/x/tokenization/types"
)

// CircuitBreakerDecorator rejects messages when the authority holds
// the corresponding halt-token in the designated collection.
type CircuitBreakerDecorator struct {
	tokenizationKeeper tokenizationkeeper.Keeper
	haltCollectionId   sdkmath.Uint
	authorityAddress   string
	msgTypeToTokenId   map[string]sdkmath.Uint // e.g. "/cosmos.bank.v1beta1.MsgSend" -> 1
}

func NewCircuitBreakerDecorator(
	tk tokenizationkeeper.Keeper,
	haltCollectionId sdkmath.Uint,
	authorityAddress string,
	msgTypeToTokenId map[string]sdkmath.Uint,
) CircuitBreakerDecorator {
	return CircuitBreakerDecorator{
		tokenizationKeeper: tk,
		haltCollectionId:   haltCollectionId,
		authorityAddress:   authorityAddress,
		msgTypeToTokenId:   msgTypeToTokenId,
	}
}

func (cbd CircuitBreakerDecorator) AnteHandle(
	ctx sdk.Context, tx sdk.Tx, simulate bool, next sdk.AnteHandler,
) (sdk.Context, error) {
	collection, found := cbd.tokenizationKeeper.GetCollectionFromStore(ctx, cbd.haltCollectionId)
	if !found {
		// Collection not created yet -- nothing to halt
		return next(ctx, tx, simulate)
	}

	balanceStore, _, err := cbd.tokenizationKeeper.GetBalanceOrApplyDefault(ctx, collection, cbd.authorityAddress)
	if err != nil {
		return next(ctx, tx, simulate)
	}

	now := sdkmath.NewUint(uint64(ctx.BlockTime().UnixMilli()))
	nowRange := []*tokenizationtypes.UintRange{{Start: now, End: now}}

	for _, msg := range tx.GetMsgs() {
		msgType := sdk.MsgTypeURL(msg)
		tokenId, ok := cbd.msgTypeToTokenId[msgType]
		if !ok {
			continue
		}

		tokenIdRange := []*tokenizationtypes.UintRange{{Start: tokenId, End: tokenId}}
		balances, err := tokenizationtypes.GetBalancesForIds(ctx, tokenIdRange, nowRange, balanceStore.Balances)
		if err == nil && len(balances) > 0 && balances[0].Amount.GT(sdkmath.NewUint(0)) {
			return ctx, fmt.Errorf("circuit breaker: %s is currently disabled", msgType)
		}
	}

	return next(ctx, tx, simulate)
}
```

Don't forget to add `"fmt"` to your imports.

## Wiring It Up in app.go

Add the decorator to your ante handler chain alongside the other decorators:

```go
// In app.go, where you build the AnteHandler:
msgTypeToTokenId := map[string]sdkmath.Uint{
	"/cosmos.bank.v1beta1.MsgSend":         sdkmath.NewUint(1),
	"/ibc.applications.transfer.v1.MsgTransfer": sdkmath.NewUint(2),
	// Add more message types as needed
}

circuitBreaker := NewCircuitBreakerDecorator(
	app.BadgesKeeper,
	sdkmath.NewUint(42), // your halt-token collection ID
	"bb1authority...",    // the address that holds halt-tokens
	msgTypeToTokenId,
)

anteHandler, err := sdk.ChainAnteDecorators(
	// ... your existing decorators ...
	circuitBreaker,
	// ... remaining decorators ...
)
```

## Free Superpowers

Because the halt-token is a regular `x/tokenization` token, you get these capabilities for free -- no extra code:

**Time-dependent halt.** Mint the halt-token with an expiring ownership time range. The message type auto-re-enables when the ownership window closes.

**Multi-sig to halt.** Put a voting challenge on the halt-token's mint approval. Halting now requires N-of-M approval before the token can be minted.

**Per-address circuit breaking.** Instead of checking a single authority address, check the sender of the transaction. You can disable specific addresses from sending specific message types by transferring halt-tokens to their address.

**Graduated response.** Use different token IDs for "rate-limited" vs "fully disabled." Your ante handler can read the balance amount to decide severity (e.g., balance of 1 = rate-limited, balance of 2 = fully blocked).

**Audit trail.** Every mint and burn of the halt-token is an on-chain event. You get a complete, queryable history of when circuit breakers were engaged and disengaged without building any additional logging.
