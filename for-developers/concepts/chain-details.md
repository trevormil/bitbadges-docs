# ✏ Chain Details

## Cosmos Chain IDs

- **Mainnet**: `bitbadges-1`
- **Testnet**: `bitbadges-2`

## EVM Chain IDs

- **Mainnet**: `50024` (claimed in ethereum-lists/chains registry)
- **Testnet**: `50025` (claimed in ethereum-lists/chains registry)
- **Local Development**: `90123`

## Coin Denomination

[Cosmos SDK Coin Denom](https://docs.cosmos.network/main/modules/bank) - "ubadge" (1 BADGE = 1 \* 10^9 ubadge)

- **Base denom**: `ubadge`
- **Display denom**: `BADGE`
- **Decimals**: 9
- **Conversion**: 1 BADGE = 1 \* 10^9 ubadge

## EVM Decimal Compatibility (precisebank Module)

For EVM compatibility, BitBadges uses the **precisebank module** to provide 18 decimal compatibility, which is the standard for Ethereum-based tokens.

**Key Units:**
- **BADGE**: 1 BADGE = 1 \* 10^18 (18 decimals in EVM)
- **ubadge**: 1 ubadge = 1 \* 10^9 (9 decimals in Cosmos)
- **abadge**: Extended fractional unit - base unit with 0 decimals in EVM
  - 1 abadge = 1 \* 10^0 (smallest unit in EVM)
  - 1 BADGE = 1 \* 10^18 abadge
  - 1 ubadge = 1 \* 10^9 abadge

**For Developers:**
When working with EVM contracts or precompiles, handle amounts accordingly:
- In EVM/Solidity: Use 18 decimal precision (1 BADGE = 1 \* 10^18 wei-equivalent)
- In Cosmos SDK: Use 9 decimal precision (1 BADGE = 1 \* 10^9 ubadge)
- The precisebank module handles the conversion between these representations automatically
