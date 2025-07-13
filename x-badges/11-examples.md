# Examples

This document provides practical examples and common usage patterns for the badges module, demonstrating how to implement various badge-based applications and workflows.

## Table of Contents

- [Basic Badge Collection](#basic-badge-collection)
- [Certificate System](#certificate-system)
- [Event Tickets](#event-tickets)
- [Gaming Achievements](#gaming-achievements)
- [Loyalty Program](#loyalty-program)
- [DAO Membership](#dao-membership)
- [Cross-Chain Integration](#cross-chain-integration)
- [Advanced Approval Patterns](#advanced-approval-patterns)

## Basic Badge Collection

A simple collectible badge series with transferable badges.

### Creating the Collection

```json
{
  "creator": "bb1creator123...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [{"start": "1", "end": "1000"}],
  "defaultBalances": {
    "balances": [
      {
        "amount": [{"start": "0", "end": "0"}],
        "badgeIds": [{"start": "1", "end": "1000"}],
        "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
      }
    ],
    "autoApproveSelfInitiatedOutgoingTransfers": true,
    "autoApproveSelfInitiatedIncomingTransfers": true
  },
  "managerTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "manager": "bb1creator123..."
    }
  ],
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "Cool Collectibles",
        "description": "A series of 1000 unique collectible badges",
        "image": "https://example.com/collection-image.png",
        "attributes": []
      }
    }
  ],
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeMetadata": [
        {
          "badgeIds": [{"start": "1", "end": "1000"}],
          "metadata": {
            "name": "Collectible #{badgeId}",
            "description": "Unique collectible badge number {badgeId}",
            "image": "https://example.com/badge-{badgeId}.png",
            "attributes": [
              {"trait_type": "Rarity", "value": "Common"}
            ]
          }
        }
      ]
    }
  ],
  "collectionPermissions": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "canUpdateCollectionMetadata": [
        {
          "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
          "permanentlyPermittedTimes": [{"start": "1", "end": "9223372036854775807"}],
          "permanentlyForbiddenTimes": []
        }
      ],
      "canCreateMoreBadges": [
        {
          "permanentlyPermittedTimes": [],
          "permanentlyForbiddenTimes": [{"start": "1", "end": "9223372036854775807"}]
        }
      ]
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "general-transfer",
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": ["*"]},
      "initiatedByList": {"addresses": ["*"]},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "1000"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {}
    }
  ]
}
```

### Minting Badges to Users

```json
{
  "creator": "bb1creator123...",
  "collectionId": "1",
  "transfers": [
    {
      "from": "Mint",
      "toAddresses": ["bb1user1...", "bb1user2...", "bb1user3..."],
      "balances": [
        {
          "amount": [{"start": "1", "end": "1"}],
          "badgeIds": [{"start": "1", "end": "3"}],
          "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
        }
      ]
    }
  ]
}
```

## Certificate System

Non-transferable certificates with expiration dates and prerequisite verification.

### Certificate Collection Setup

```json
{
  "creator": "bb1university...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [{"start": "1", "end": "999999"}],
  "defaultBalances": {
    "balances": [
      {
        "amount": [{"start": "0", "end": "0"}],
        "badgeIds": [{"start": "1", "end": "999999"}],
        "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
      }
    ],
    "autoApproveSelfInitiatedOutgoingTransfers": false,
    "autoApproveSelfInitiatedIncomingTransfers": false
  },
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "University Certificates",
        "description": "Official academic certificates and achievements",
        "image": "https://university.edu/certificate-seal.png"
      }
    }
  ],
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeMetadata": [
        {
          "badgeIds": [{"start": "1", "end": "1000"}],
          "metadata": {
            "name": "Computer Science Degree",
            "description": "Bachelor of Science in Computer Science",
            "image": "https://university.edu/cs-degree.png"
          }
        },
        {
          "badgeIds": [{"start": "1001", "end": "2000"}],
          "metadata": {
            "name": "Advanced Programming Certificate",
            "description": "Certificate in Advanced Programming Techniques",
            "image": "https://university.edu/programming-cert.png"
          }
        }
      ]
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "no-transfers",
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": []},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "999999"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
    }
  ],
  "collectionPermissions": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "canCreateMoreBadges": [
        {
          "permanentlyPermittedTimes": [{"start": "1", "end": "9223372036854775807"}],
          "permanentlyForbiddenTimes": []
        }
      ]
    }
  ]
}
```

### Issuing Certificates with Prerequisites

```json
{
  "creator": "bb1university...",
  "collectionId": "2",
  "transfers": [
    {
      "from": "Mint",
      "toAddresses": ["bb1student..."],
      "balances": [
        {
          "amount": [{"start": "1", "end": "1"}],
          "badgeIds": [{"start": "1001", "end": "1001"}],
          "ownershipTimes": [
            {
              "start": "1672531200000",
              "end": "1830297600000"
            }
          ]
        }
      ],
      "merkleProofs": {
        "merkleProofs": [
          {
            "leaf": "bb1student...",
            "proof": ["0x123...", "0x456..."],
            "aunts": []
          }
        ]
      }
    }
  ]
}
```

## Event Tickets

Time-limited tickets with restricted transferability and attendance verification.

### Event Ticket Collection

```json
{
  "creator": "bb1eventorganizer...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [
    {"start": "1", "end": "100"},
    {"start": "101", "end": "150"},
    {"start": "151", "end": "200"}
  ],
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "TechConf 2024 Tickets",
        "description": "Official tickets for TechConf 2024",
        "image": "https://techconf.com/2024-logo.png"
      }
    }
  ],
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeMetadata": [
        {
          "badgeIds": [{"start": "1", "end": "100"}],
          "metadata": {
            "name": "General Admission",
            "description": "General admission to TechConf 2024",
            "attributes": [
              {"trait_type": "Ticket Type", "value": "General"},
              {"trait_type": "Event Date", "value": "2024-06-15"}
            ]
          }
        },
        {
          "badgeIds": [{"start": "101", "end": "150"}],
          "metadata": {
            "name": "VIP Access",
            "description": "VIP access with backstage privileges",
            "attributes": [
              {"trait_type": "Ticket Type", "value": "VIP"},
              {"trait_type": "Event Date", "value": "2024-06-15"}
            ]
          }
        }
      ]
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "ticket-transfers",
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": ["*"]},
      "transferTimes": [
        {
          "start": "1672531200000",
          "end": "1718409600000"
        }
      ],
      "badgeIds": [{"start": "1", "end": "200"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "maxNumTransfers": [
          {
            "maxNumTransfers": "2",
            "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
            "badgeIds": [{"start": "1", "end": "200"}],
            "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
          }
        ]
      }
    }
  ]
}
```

### Ticket Purchase with Dynamic Store

```json
{
  "creator": "bb1eventorganizer...",
  "defaultValue": false
}
```

```json
{
  "creator": "bb1eventorganizer...",
  "storeId": "1",
  "address": "bb1purchaser...",
  "value": true
}
```

```json
{
  "creator": "bb1eventorganizer...",
  "collectionId": "3",
  "transfers": [
    {
      "from": "Mint",
      "toAddresses": ["bb1purchaser..."],
      "balances": [
        {
          "amount": [{"start": "1", "end": "1"}],
          "badgeIds": [{"start": "1", "end": "1"}],
          "ownershipTimes": [
            {
              "start": "1672531200000",
              "end": "1718582400000"
            }
          ]
        }
      ]
    }
  ]
}
```

## Gaming Achievements

Progressive achievement system with unlockable tiers and seasonal rewards.

### Gaming Achievement Collection

```json
{
  "creator": "bb1gamedev...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [
    {"start": "1", "end": "50"},
    {"start": "51", "end": "100"},
    {"start": "101", "end": "150"}
  ],
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "Epic Quest Achievements",
        "description": "Achievement badges for Epic Quest game",
        "image": "https://epicquest.game/achievements-banner.png"
      }
    }
  ],
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeMetadata": [
        {
          "badgeIds": [{"start": "1", "end": "50"}],
          "metadata": {
            "name": "Bronze Achievement",
            "description": "Basic level achievement",
            "attributes": [
              {"trait_type": "Tier", "value": "Bronze"},
              {"trait_type": "Difficulty", "value": "Easy"}
            ]
          }
        },
        {
          "badgeIds": [{"start": "51", "end": "100"}],
          "metadata": {
            "name": "Silver Achievement",
            "description": "Intermediate level achievement",
            "attributes": [
              {"trait_type": "Tier", "value": "Silver"},
              {"trait_type": "Difficulty", "value": "Medium"}
            ]
          }
        }
      ]
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "achievement-unlock",
      "fromList": {"addresses": ["Mint"]},
      "toList": {"addresses": ["*"]},
      "initiatedByList": {"addresses": ["bb1gameserver..."]},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "150"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "mustOwnBadges": [
          {
            "collectionId": "4",
            "amountRange": [{"start": "1", "end": "999999"}],
            "badgeIds": [{"start": "1", "end": "50"}],
            "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
            "mustSatisfyForSpecificAddress": "bb1player..."
          }
        ]
      }
    }
  ]
}
```

## Loyalty Program

Points-based system with tier progression and reward redemption.

### Loyalty Points Collection

```json
{
  "creator": "bb1retailer...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [{"start": "1", "end": "1"}],
  "defaultBalances": {
    "balances": [
      {
        "amount": [{"start": "0", "end": "0"}],
        "badgeIds": [{"start": "1", "end": "1"}],
        "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
      }
    ]
  },
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "Store Loyalty Points",
        "description": "Loyalty points for purchases and rewards",
        "image": "https://store.com/loyalty-logo.png"
      }
    }
  ],
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeMetadata": [
        {
          "badgeIds": [{"start": "1", "end": "1"}],
          "metadata": {
            "name": "Loyalty Points",
            "description": "Redeemable loyalty points",
            "attributes": [
              {"trait_type": "Type", "value": "Points"},
              {"trait_type": "Redeemable", "value": "Yes"}
            ]
          }
        }
      ]
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "points-earn",
      "fromList": {"addresses": ["Mint"]},
      "toList": {"addresses": ["*"]},
      "initiatedByList": {"addresses": ["bb1retailer..."]},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "1"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
    },
    {
      "approvalId": "points-redeem",
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": ["bb1retailer..."]},
      "initiatedByList": {"addresses": ["*"]},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "1"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "approvalAmounts": [
          {
            "approvalId": "redemption-limit",
            "amountTrackerId": [{"start": "1", "end": "1"}],
            "overallApprovalAmount": "1000",
            "perFromAddressApprovalAmount": "100",
            "perToAddressApprovalAmount": "100",
            "perInitiatedByAddressApprovalAmount": "100"
          }
        ]
      }
    }
  ]
}
```

### Awarding Points for Purchase

```json
{
  "creator": "bb1retailer...",
  "collectionId": "5",
  "transfers": [
    {
      "from": "Mint",
      "toAddresses": ["bb1customer..."],
      "balances": [
        {
          "amount": [{"start": "50", "end": "50"}],
          "badgeIds": [{"start": "1", "end": "1"}],
          "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
        }
      ],
      "memo": "Purchase reward: Order #12345"
    }
  ]
}
```

## DAO Membership

Governance tokens with voting power and delegation capabilities.

### DAO Membership Collection

```json
{
  "creator": "bb1dao...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [
    {"start": "1", "end": "1"},
    {"start": "2", "end": "2"},
    {"start": "3", "end": "3"}
  ],
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "TechDAO Membership",
        "description": "Membership and voting tokens for TechDAO",
        "image": "https://techdao.org/logo.png"
      }
    }
  ],
  "badgeMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeMetadata": [
        {
          "badgeIds": [{"start": "1", "end": "1"}],
          "metadata": {
            "name": "Basic Member",
            "description": "Basic DAO membership with 1 vote per token",
            "attributes": [
              {"trait_type": "Voting Power", "value": "1"},
              {"trait_type": "Member Type", "value": "Basic"}
            ]
          }
        },
        {
          "badgeIds": [{"start": "2", "end": "2"}],
          "metadata": {
            "name": "Premium Member",
            "description": "Premium DAO membership with 5 votes per token",
            "attributes": [
              {"trait_type": "Voting Power", "value": "5"},
              {"trait_type": "Member Type", "value": "Premium"}
            ]
          }
        },
        {
          "badgeIds": [{"start": "3", "end": "3"}],
          "metadata": {
            "name": "Founder",
            "description": "Founder membership with 10 votes per token",
            "attributes": [
              {"trait_type": "Voting Power", "value": "10"},
              {"trait_type": "Member Type", "value": "Founder"}
            ]
          }
        }
      ]
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "membership-transfer",
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": ["*"]},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "3"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "mustOwnBadges": [
          {
            "collectionId": "6",
            "amountRange": [{"start": "1", "end": "999999"}],
            "badgeIds": [{"start": "1", "end": "3"}],
            "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
            "mustSatisfyForSpecificAddress": "to"
          }
        ]
      }
    }
  ]
}
```

## Cross-Chain Integration

Cross-chain badge transfers using IBC with multi-chain signature support.

### Cross-Chain Collection Setup

```json
{
  "creator": "bb1multichain...",
  "balancesType": "Standard",
  "badgeIdsToAdd": [{"start": "1", "end": "10000"}],
  "collectionMetadataTimeline": [
    {
      "timelineTimes": [{"start": "1", "end": "9223372036854775807"}],
      "collectionMetadata": {
        "name": "Cross-Chain Collectibles",
        "description": "Collectibles that work across multiple blockchains",
        "image": "https://multichain.com/cross-chain-logo.png"
      }
    }
  ],
  "collectionApprovals": [
    {
      "approvalId": "cross-chain-transfer",
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": ["*"]},
      "transferTimes": [{"start": "1", "end": "9223372036854775807"}],
      "badgeIds": [{"start": "1", "end": "10000"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "coinTransferRequirement": [
          {
            "coin": {
              "denom": "uatom",
              "amount": "1000"
            },
            "toAddress": "bb1feecollector...",
            "fromAddress": "from"
          }
        ]
      }
    }
  ]
}
```

## Advanced Approval Patterns

Complex approval configurations for sophisticated use cases.

### Whitelist with Merkle Tree

```json
{
  "creator": "bb1creator...",
  "addressLists": [
    {
      "addresses": ["0x123...", "0x456...", "0x789..."],
      "whitelist": true,
      "uri": "https://example.com/whitelist-metadata",
      "customData": "Early access whitelist"
    }
  ]
}
```

### Collection with Merkle Challenge

```json
{
  "collectionApprovals": [
    {
      "approvalId": "whitelist-mint",
      "fromList": {"addresses": ["Mint"]},
      "toList": {"listId": "1"},
      "transferTimes": [{"start": "1672531200000", "end": "1675209600000"}],
      "badgeIds": [{"start": "1", "end": "1000"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "merkleChallenge": [
          {
            "challengeId": "whitelist-challenge",
            "expectedProofLength": [{"start": "3", "end": "3"}],
            "root": "0xabcdef...",
            "useCreatorAddressAsLeaf": true,
            "maxUsesPerLeaf": [{"start": "1", "end": "1"}]
          }
        ]
      }
    }
  ]
}
```

### Time-Limited Transfers with Usage Tracking

```json
{
  "collectionApprovals": [
    {
      "approvalId": "limited-transfer",
      "amountTrackerId": [{"start": "1", "end": "1"}],
      "fromList": {"addresses": ["*"]},
      "toList": {"addresses": ["*"]},
      "transferTimes": [{"start": "1672531200000", "end": "1675209600000"}],
      "badgeIds": [{"start": "1", "end": "100"}],
      "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}],
      "approvalCriteria": {
        "approvalAmounts": [
          {
            "approvalId": "transfer-limit",
            "amountTrackerId": [{"start": "1", "end": "1"}],
            "overallApprovalAmount": "1000",
            "perFromAddressApprovalAmount": "10",
            "perToAddressApprovalAmount": "10"
          }
        ],
        "maxNumTransfers": [
          {
            "maxNumTransfers": "100",
            "transferTimes": [{"start": "1672531200000", "end": "1675209600000"}],
            "badgeIds": [{"start": "1", "end": "100"}],
            "ownershipTimes": [{"start": "1", "end": "9223372036854775807"}]
          }
        ]
      }
    }
  ]
}
```

These examples demonstrate the flexibility and power of the badges module for implementing various digital asset use cases, from simple collectibles to complex governance and incentive systems.