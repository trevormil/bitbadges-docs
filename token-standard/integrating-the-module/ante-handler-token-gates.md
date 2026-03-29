# Ante Handler Token Gates

Any Cosmos SDK chain that integrates `x/tokenization` can gate **any message type** on token ownership using a single ante handler decorator. The pattern is simple: a stateless decorator checks whether the transaction sender (or any configured address) holds a specific token before allowing a message through. All policy state lives in token ownership -- no new store, no migration, no custom module.

This turns `x/tokenization` tokens into a **programmable policy engine**. KYC credentials, accreditation status, circuit breaker signals, governance eligibility -- they are all just token balances checked at the ante handler level.

## The Decorator

The `ComplianceAnteDecorator` below is a generic, configurable decorator. You define a map of message type to token requirements, and it enforces them. Every use case on this page is just a different configuration of this same decorator.

```go
package app

import (
	"fmt"

	sdkmath "cosmossdk.io/math"
	sdk "github.com/cosmos/cosmos-sdk/types"
	tokenizationkeeper "github.com/bitbadges/bitbadgeschain/x/tokenization/keeper"
	tokenizationtypes "github.com/bitbadges/bitbadgeschain/x/tokenization/types"
)

// TokenRequirement defines what token must be held for a message type to be allowed.
type TokenRequirement struct {
	CollectionId   sdkmath.Uint
	TokenId        sdkmath.Uint
	MinBalance     sdkmath.Uint
	CheckAddress   string // If empty, checks the transaction sender
	MustHold       bool   // true = must hold token to proceed; false = must NOT hold (circuit breaker)
	ErrorMsg       string
}

// ComplianceAnteDecorator gates message types on token ownership.
type ComplianceAnteDecorator struct {
	tokenizationKeeper tokenizationkeeper.Keeper
	requirements       map[string][]TokenRequirement // msg type URL -> requirements
}

func NewComplianceAnteDecorator(
	tk tokenizationkeeper.Keeper,
	requirements map[string][]TokenRequirement,
) ComplianceAnteDecorator {
	return ComplianceAnteDecorator{
		tokenizationKeeper: tk,
		requirements:       requirements,
	}
}

func (cad ComplianceAnteDecorator) AnteHandle(
	ctx sdk.Context, tx sdk.Tx, simulate bool, next sdk.AnteHandler,
) (sdk.Context, error) {
	now := sdkmath.NewUint(uint64(ctx.BlockTime().UnixMilli()))
	nowRange := []*tokenizationtypes.UintRange{{Start: now, End: now}}

	// Get the fee payer (transaction sender)
	feeTx, ok := tx.(sdk.FeeTx)
	var sender string
	if ok {
		sender = sdk.AccAddress(feeTx.FeePayer()).String()
	}

	for _, msg := range tx.GetMsgs() {
		msgType := sdk.MsgTypeURL(msg)
		reqs, exists := cad.requirements[msgType]
		if !exists {
			continue
		}

		for _, req := range reqs {
			checkAddr := req.CheckAddress
			if checkAddr == "" {
				checkAddr = sender
			}
			if checkAddr == "" {
				continue
			}

			collection, found := cad.tokenizationKeeper.GetCollectionFromStore(ctx, req.CollectionId)
			if !found {
				if req.MustHold {
					return ctx, fmt.Errorf("%s", req.ErrorMsg)
				}
				continue
			}

			balanceStore, _, err := cad.tokenizationKeeper.GetBalanceOrApplyDefault(ctx, collection, checkAddr)
			if err != nil {
				if req.MustHold {
					return ctx, fmt.Errorf("%s", req.ErrorMsg)
				}
				continue
			}

			tokenIdRange := []*tokenizationtypes.UintRange{{Start: req.TokenId, End: req.TokenId}}
			balances, err := tokenizationtypes.GetBalancesForIds(ctx, tokenIdRange, nowRange, balanceStore.Balances)

			hasBalance := err == nil && len(balances) > 0 && balances[0].Amount.GTE(req.MinBalance)

			if req.MustHold && !hasBalance {
				return ctx, fmt.Errorf("%s", req.ErrorMsg)
			}
			if !req.MustHold && hasBalance {
				return ctx, fmt.Errorf("%s", req.ErrorMsg)
			}
		}
	}

	return next(ctx, tx, simulate)
}
```

## Wiring It Up in app.go

Add the decorator to your ante handler chain. The `requirements` map is where you define your chain's policies:

```go
// In app.go, where you build the AnteHandler:
requirements := map[string][]TokenRequirement{
	// Add your requirements here (see examples below)
}

complianceDecorator := NewComplianceAnteDecorator(
	app.BadgesKeeper,
	requirements,
)

anteHandler, err := sdk.ChainAnteDecorators(
	// ... your existing decorators ...
	complianceDecorator,
	// ... remaining decorators ...
)
```

Each use case below shows example `requirements` entries you plug into this same map.

## Circuit Breaker (Replacing x/circuit)

The Cosmos SDK `x/circuit` module is deprecated. Replace it by creating a "halt token" collection where each token ID maps to a message type. When the authority address holds the halt-token, that message type is rejected. Burn the token (or let it expire) to re-enable.

This uses `MustHold: false` -- the message is blocked when the token **is** held.

```go
// Circuit breaker: block messages when authority holds halt-token
"/cosmos.bank.v1beta1.MsgSend": {
	{
		CollectionId: sdkmath.NewUint(42),
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "bb1authority...", // fixed address, not sender
		MustHold:     false,            // must NOT hold -> message allowed
		ErrorMsg:     "circuit breaker: MsgSend is currently disabled",
	},
},
"/ibc.applications.transfer.v1.MsgTransfer": {
	{
		CollectionId: sdkmath.NewUint(42),
		TokenId:      sdkmath.NewUint(2),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "bb1authority...",
		MustHold:     false,
		ErrorMsg:     "circuit breaker: IBC transfers are currently disabled",
	},
},
```

**How it works:** Mint a halt-token to the authority address to disable a message type. Burn it to re-enable. The decorator does the rest.

## KYC-Gated Transfers

Require the transaction sender to hold a KYC credential token before they can send funds. Create a KYC collection where token ID 1 = basic KYC and token ID 2 = enhanced KYC. Issue tokens to users after identity verification.

```go
// Basic KYC required for all bank sends
"/cosmos.bank.v1beta1.MsgSend": {
	{
		CollectionId: sdkmath.NewUint(100),  // KYC credential collection
		TokenId:      sdkmath.NewUint(1),    // basic KYC token
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",                    // empty = check tx sender
		MustHold:     true,
		ErrorMsg:     "KYC credential required to send funds",
	},
},
// Enhanced KYC for multi-send (large/batch transfers)
"/cosmos.bank.v1beta1.MsgMultiSend": {
	{
		CollectionId: sdkmath.NewUint(100),
		TokenId:      sdkmath.NewUint(2),    // enhanced KYC token
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "enhanced KYC credential required for multi-send",
	},
},
```

**Tiered KYC.** Use different token IDs for different verification levels. Basic KYC for standard transfers, enhanced KYC for large or batch operations. The credential issuer controls who gets which tier by minting the appropriate token.

## Compliant Staking

On regulated chains, restrict staking to accredited investors. Require the sender to hold an accreditation credential token before delegating to a validator.

```go
// Accredited investor credential required to stake
"/cosmos.staking.v1beta1.MsgDelegate": {
	{
		CollectionId: sdkmath.NewUint(200),  // accreditation collection
		TokenId:      sdkmath.NewUint(1),    // accredited investor token
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "accredited investor credential required to stake",
	},
},
// Also gate redelegation and unbonding if needed
"/cosmos.staking.v1beta1.MsgBeginRedelegate": {
	{
		CollectionId: sdkmath.NewUint(200),
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "accredited investor credential required to redelegate",
	},
},
```

## IBC Transfer Compliance

Gate cross-chain transfers on credential ownership. Useful for chains that need to ensure only verified users move assets to other chains.

```go
// KYC required for cross-chain sends
"/ibc.applications.transfer.v1.MsgTransfer": {
	{
		CollectionId: sdkmath.NewUint(100),  // same KYC collection
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "KYC credential required for IBC transfers",
	},
},
```

This prevents unverified users from bridging assets to other chains where your compliance controls may not apply.

## Governance Participation Gates

Require credential tokens to vote on governance proposals. For security token governance, this ensures only compliant holders participate in on-chain decisions.

```go
// Credential required to vote
"/cosmos.gov.v1.MsgVote": {
	{
		CollectionId: sdkmath.NewUint(300),  // governance eligibility collection
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "governance credential required to vote",
	},
},
// Also gate weighted votes
"/cosmos.gov.v1.MsgVoteWeighted": {
	{
		CollectionId: sdkmath.NewUint(300),
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "governance credential required to vote",
	},
},
```

## Custom Chain Policies

The examples above are starting points. The decorator is completely open -- you can gate **any** message type on **any** token requirement. Some ideas:

- **Authz grants:** Gate `MsgGrant` so only credentialed addresses can delegate permissions.
- **Contract execution:** Gate `MsgExecuteContract` on a developer license token.
- **Fee grants:** Gate `MsgGrantAllowance` on an institutional credential.

You can also stack multiple requirements on the same message type. A single `MsgSend` can require both a KYC token **and** not be circuit-broken:

```go
"/cosmos.bank.v1beta1.MsgSend": {
	{
		CollectionId: sdkmath.NewUint(100),  // KYC
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "",
		MustHold:     true,
		ErrorMsg:     "KYC credential required to send funds",
	},
	{
		CollectionId: sdkmath.NewUint(42),   // circuit breaker
		TokenId:      sdkmath.NewUint(1),
		MinBalance:   sdkmath.NewUint(1),
		CheckAddress: "bb1authority...",
		MustHold:     false,
		ErrorMsg:     "circuit breaker: MsgSend is currently disabled",
	},
},
```

## Free Superpowers

Because every credential and signal is a regular `x/tokenization` token, you get these capabilities for free -- no extra code in your ante handler:

**Time-dependent credentials.** Mint a KYC token with an expiring ownership time range. The credential auto-revokes when the window closes. No cron job, no expiry check -- the balance query already factors in time.

**Auto-resuming circuit breakers.** Mint a halt-token with a time range. The message type auto-re-enables when the ownership window ends.

**Multi-sig activation.** Put a voting challenge on a token's mint approval. Halting the chain, issuing a credential, or revoking access now requires N-of-M approval before the token can be minted or burned.

**Revocable credentials.** The credential issuer can burn or transfer tokens to revoke access instantly. No need for a separate revocation registry.

**Graduated response.** Use `MinBalance` to implement severity levels. For a circuit breaker: balance of 1 = rate-limited, balance of 2 = fully blocked. For KYC: balance of 1 = basic, balance of 2 = enhanced.

**Per-address policies.** Leave `CheckAddress` empty to check the transaction sender. Set it to a fixed address for global policies (circuit breaker). Mix both in the same configuration.

**Audit trail.** Every mint, burn, and transfer of credential tokens is an on-chain event. You get a complete, queryable history of policy changes without building any additional logging.
