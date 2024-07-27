# ZK Proofs

Zero knowledge proofs are also supported in the approval interface. If you provide a valid proof, you are granted approval.&#x20;

Pre-Readings: [https://docs.circom.io/background/background/](https://docs.circom.io/background/background/) and have a basic understanding of the concepts of zkSnarks and ZK Proofs from a high level. You do not need to fully know the math / cryptography behind the scenes to write ZK circuits, but you should be familiar with the main concepts.

**Replays**

Each valid proof solution can only be used once. This is tracked with the **zkpTrackerId**, in the same manner as merkle challenges (we refer you there for more info). The tracker is scoped to an approval, immutable, and increment only (full ID -> USED or UNUSED). Design your proofs with this in consideration.

**Storage**

On the blockchain, we store an array of proofs that need to be solved for each approval (if array is empty, there is no need). Users must solve all the proofs in the array for approval. This is stored in the approval's **approvalCriteria.zkProofs.** See the iZkProof interface below. All that we store on-chain (for scalability reasons) is the verification key. You may provide additional details (such as the circuit, proving key, etc) via the uri or customData.&#x20;

```typescript
export interface iZkProof {
    /**
     * The verification key of the zkProof.
     */
    verificationKey: string;

    /**
     * The URI where to fetch the zkProof metadata from.
     */
    uri: string;

    /**
     * Arbitrary custom data that can be stored on-chain.
     */
    customData: string;

    /**
     * ZKP tracker ID.
     */
    zkpTrackerId: string;
}
```

**Proof Solutions**

Within MsgTransferBadges, users can specify **zkProofSolutions** array where they provide the valid solutions to the proofs. These follow the interface as seen below.

```
export interface iZkProofSolution {
  /**
   * The proof of the zkProof.
   */
  proof: string;

  /**
   * The public inputs of the zkProof.
   */
  publicInputs: string;
}
```

**Formatting**

We rely heavily on [Circom implementation](https://docs.circom.io/getting-started/installation/) for proofs and formatting. Circom is a library and full programming language to write ZK proofs. Pretty much, you will do all the proof writing, generation, and proving with Circom. Then, the outputted files are what we use on-chain.

Proving Method: zkSnark Groth16

```
pragma circom 2.0.0;

/*This circuit template checks that c is the multiplication of a and b.*/

template Multiplier2 () {

   // Declaration of signals.
   signal input a;
   signal input b;
   signal output c;

   // Constraints.
   c <== a * b;
}
```

With every compiled Circom circuit + valid proof, you will get the following files:

-   verification_key.json - The verification key for proving the circuit
-   proof.json - A succint proof proving all criteria is satisfied
-   public.json - The public inputs to the proof.

These JSON files should be stringified, and they are what is passed on-chain via storage and the solutions.

The verificationKey.json is what you will pass into verificationKey field of iZkProof.

The proof.json is what you will pass into proof of iZkProofSolution.

The public.json is what you will pass into publicInputs of iZkProofSolution.

Examples

<figure><img src="../../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

Create Collection Msg w/ Proof

```json
{
    "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
    "collectionId": "0",
    "balancesType": "Standard",
    "defaultBalances": {
        "balances": [],
        "incomingApprovals": [],
        "outgoingApprovals": [],
        "userPermissions": {
            "canUpdateOutgoingApprovals": [],
            "canUpdateIncomingApprovals": [],
            "canUpdateAutoApproveSelfInitiatedOutgoingTransfers": [],
            "canUpdateAutoApproveSelfInitiatedIncomingTransfers": []
        },
        "autoApproveSelfInitiatedOutgoingTransfers": true,
        "autoApproveSelfInitiatedIncomingTransfers": true
    },
    "badgeIdsToAdd": [
        {
            "start": "1",
            "end": "100"
        }
    ],
    "updateCollectionPermissions": true,
    "collectionPermissions": {
        "canDeleteCollection": [],
        "canArchiveCollection": [],
        "canUpdateOffChainBalancesMetadata": [],
        "canUpdateStandards": [],
        "canUpdateCustomData": [],
        "canUpdateManager": [],
        "canUpdateCollectionMetadata": [],
        "canUpdateValidBadgeIds": [],
        "canUpdateBadgeMetadata": [],
        "canUpdateCollectionApprovals": []
    },
    "updateManagerTimeline": true,
    "managerTimeline": [],
    "updateCollectionMetadataTimeline": true,
    "collectionMetadataTimeline": [
        {
            "collectionMetadata": {
                "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
                "customData": ""
            },
            "timelineTimes": [
                {
                    "start": "1",
                    "end": "18446744073709551615"
                }
            ]
        }
    ],
    "updateBadgeMetadataTimeline": true,
    "badgeMetadataTimeline": [
        {
            "badgeMetadata": [
                {
                    "uri": "ipfs://Qmf8xxN2fwXGgouue3qsJtN8ZRSsnoHxM9mGcynTPhh6Ub",
                    "badgeIds": [
                        {
                            "start": "1",
                            "end": "10000000000000"
                        }
                    ],
                    "customData": ""
                }
            ],
            "timelineTimes": [
                {
                    "start": "1",
                    "end": "18446744073709551615"
                }
            ]
        }
    ],
    "updateOffChainBalancesMetadataTimeline": true,
    "offChainBalancesMetadataTimeline": [],
    "updateCustomDataTimeline": true,
    "customDataTimeline": [],
    "updateCollectionApprovals": true,
    "collectionApprovals": [
        {
            "toListId": "All",
            "fromListId": "Mint",
            "initiatedByListId": "All",
            "transferTimes": [
                {
                    "start": "1",
                    "end": "18446744073709551615"
                }
            ],
            "badgeIds": [
                {
                    "start": "1",
                    "end": "100"
                }
            ],
            "ownershipTimes": [
                {
                    "start": "1",
                    "end": "18446744073709551615"
                }
            ],
            "approvalId": "test",
            "amountTrackerId": "fdklasf",
            "challengeTrackerId": "fdhhjksafhdj",
            "uri": "ipfs://QmWtjFHDbKCKNAMoJH3ZAkt6eCY8RSohHpXFMF9yf8vAuN",
            "customData": "",
            "approvalCriteria": {
                "mustOwnBadges": [],
                "merkleChallenge": {
                    "root": "",
                    "expectedProofLength": "0",
                    "useCreatorAddressAsLeaf": false,
                    "maxUsesPerLeaf": "0",
                    "uri": "",
                    "customData": ""
                },
                "predeterminedBalances": {
                    "manualBalances": [],
                    "incrementedBalances": {
                        "startBalances": [],
                        "incrementBadgeIdsBy": "0",
                        "incrementOwnershipTimesBy": "0"
                    },
                    "orderCalculationMethod": {
                        "useOverallNumTransfers": false,
                        "usePerToAddressNumTransfers": false,
                        "usePerFromAddressNumTransfers": false,
                        "usePerInitiatedByAddressNumTransfers": false,
                        "useMerkleChallengeLeafIndex": false
                    }
                },
                "approvalAmounts": {
                    "overallApprovalAmount": "0",
                    "perToAddressApprovalAmount": "0",
                    "perFromAddressApprovalAmount": "0",
                    "perInitiatedByAddressApprovalAmount": "0"
                },
                "maxNumTransfers": {
                    "overallMaxNumTransfers": "0",
                    "perToAddressMaxNumTransfers": "0",
                    "perFromAddressMaxNumTransfers": "0",
                    "perInitiatedByAddressMaxNumTransfers": "1"
                },
                "requireToEqualsInitiatedBy": false,
                "requireFromEqualsInitiatedBy": false,
                "requireToDoesNotEqualInitiatedBy": false,
                "requireFromDoesNotEqualInitiatedBy": false,
                "overridesFromOutgoingApprovals": true,
                "overridesToIncomingApprovals": true,
                "zkProofs": [
                    {
                        "verificationKey": "{\n\t\t\t\t\"protocol\": \"groth16\",\n\t\t\t\t\"curve\": \"bn128\",\n\t\t\t\t\"nPublic\": 1,\n\t\t\t\t\"vk_alpha_1\": [\n\t\t\t\t \"13148620221535972250452852483621388707005895520407749254029942589019911968317\",\n\t\t\t\t \"2889632388761537408217987365790126432839324967233397666357486962226656274862\",\n\t\t\t\t \"1\"\n\t\t\t\t],\n\t\t\t\t\"vk_beta_2\": [\n\t\t\t\t [\n\t\t\t\t\t\"235184211515192335800888829014779038630276494620926581575684953647609832787\",\n\t\t\t\t\t\"19688718941701106378739125377381200373190897484213597668400002191692567452234\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"17139505730823840138430760175182215851804207584515613019250263963726263545682\",\n\t\t\t\t\t\"15827693620421453696738510788300059898248795618831839628175348577712566794715\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"1\",\n\t\t\t\t\t\"0\"\n\t\t\t\t ]\n\t\t\t\t],\n\t\t\t\t\"vk_gamma_2\": [\n\t\t\t\t [\n\t\t\t\t\t\"10857046999023057135944570762232829481370756359578518086990519993285655852781\",\n\t\t\t\t\t\"11559732032986387107991004021392285783925812861821192530917403151452391805634\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"8495653923123431417604973247489272438418190587263600148770280649306958101930\",\n\t\t\t\t\t\"4082367875863433681332203403145435568316851327593401208105741076214120093531\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"1\",\n\t\t\t\t\t\"0\"\n\t\t\t\t ]\n\t\t\t\t],\n\t\t\t\t\"vk_delta_2\": [\n\t\t\t\t [\n\t\t\t\t\t\"503765186455257630293828867624988330153366884012427107660916834216629962594\",\n\t\t\t\t\t\"5063908373717690658921653190209557568433126368699956121277602772003448036384\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"7089519436043338270412729805469570497303116366059156310430246336448604006110\",\n\t\t\t\t\t\"4176347866589911546633049464699627263382512831672401904553239775361215104922\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"1\",\n\t\t\t\t\t\"0\"\n\t\t\t\t ]\n\t\t\t\t],\n\t\t\t\t\"vk_alphabeta_12\": [\n\t\t\t\t [\n\t\t\t\t\t[\n\t\t\t\t\t \"18604433221051551034480587482664214870599340609016463817742300813893366628865\",\n\t\t\t\t\t \"18085175124799896104522525897296636509228330955796915514958149085721253919374\"\n\t\t\t\t\t],\n\t\t\t\t\t[\n\t\t\t\t\t \"5818719788508620172684530200840038363514092465014332706277430885575255653155\",\n\t\t\t\t\t \"13271739325997130171594168052085563392477243703137959518318871241167867548541\"\n\t\t\t\t\t],\n\t\t\t\t\t[\n\t\t\t\t\t \"15990090000399229437316874893245659161037348656905875554257382661511921214897\",\n\t\t\t\t\t \"21037562072546073120182607002350315755086517195889115785617784603842266232387\"\n\t\t\t\t\t]\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t[\n\t\t\t\t\t \"2203738935953083333113402844434965170714091346913547128007885238438368497527\",\n\t\t\t\t\t \"12254441210030526054660165902009315439490317537751453916247356602839811164652\"\n\t\t\t\t\t],\n\t\t\t\t\t[\n\t\t\t\t\t \"18042350999254486153299086364647673457109976987505413008188358179267817106631\",\n\t\t\t\t\t \"20440311422039242859067644437230958996502643417500127952743189403997461532235\"\n\t\t\t\t\t],\n\t\t\t\t\t[\n\t\t\t\t\t \"3918110739729735412812703372321634476922749810769795477323113295317098326469\",\n\t\t\t\t\t \"659595211998342112032231124403258238789761295830862227926617204748855071177\"\n\t\t\t\t\t]\n\t\t\t\t ]\n\t\t\t\t],\n\t\t\t\t\"IC\": [\n\t\t\t\t [\n\t\t\t\t\t\"12090554620905913999269511296228069288923128009598574755312442527252764243545\",\n\t\t\t\t\t\"20477553370846440301046582608494182925724854012349177770405179934985848699502\",\n\t\t\t\t\t\"1\"\n\t\t\t\t ],\n\t\t\t\t [\n\t\t\t\t\t\"21363409161656661765284862807955826021689494498345713938600256958010467481555\",\n\t\t\t\t\t\"6970854698956478603764888286629038914534847038473858754877750290063128523222\",\n\t\t\t\t\t\"1\"\n\t\t\t\t ]\n\t\t\t\t]\n\t\t\t }",
                        "uri": "",
                        "customData": ""
                    }
                ]
            }
        }
    ],
    "updateStandardsTimeline": true,
    "standardsTimeline": [],
    "updateIsArchivedTimeline": true,
    "isArchivedTimeline": []
}
```

Sample Transfer Msg&#x20;

```json
{
    "creator": "cosmos1kfr2xajdvs46h0ttqadu50nhu8x4v0tcfn4p0x",
    "collectionId": "21",
    "transfers": [
        {
            "from": "Mint",
            "toAddresses": ["cosmos178m3nrv6arrfwh6r6gcr60v6m63cyxrycf4ywe"],
            "balances": [
                {
                    "amount": "1",
                    "badgeIds": [
                        {
                            "start": "1",
                            "end": "1"
                        }
                    ],
                    "ownershipTimes": [
                        {
                            "start": "1",
                            "end": "1"
                        }
                    ]
                }
            ],
            "precalculateBalancesFromApproval": {
                "approvalId": "",
                "approvalLevel": "",
                "approverAddress": ""
            },
            "merkleProofs": [],
            "memo": "",
            "prioritizedApprovals": [],
            "onlyCheckPrioritizedCollectionApprovals": false,
            "onlyCheckPrioritizedIncomingApprovals": false,
            "onlyCheckPrioritizedOutgoingApprovals": false,
            "zkProofSolutions": [
                {
                    "proof": "{\n\t\t\t\t\t\t\t\"pi_a\": [\n\t\t\t\t\t\t\t \"15280415993796597666813636493043784530691061101504618964413605220785439881335\",\n\t\t\t\t\t\t\t \"675563788996052368699527111569929717352978256610453492975963571932469065914\",\n\t\t\t\t\t\t\t \"1\"\n\t\t\t\t\t\t\t],\n\t\t\t\t\t\t\t\"pi_b\": [\n\t\t\t\t\t\t\t [\n\t\t\t\t\t\t\t\t\"13271940717404841760534790023945884845854169265312102895125858252700907827176\",\n\t\t\t\t\t\t\t\t\"591918590265443693515148586084694063044314117360908071728035775984509485618\"\n\t\t\t\t\t\t\t ],\n\t\t\t\t\t\t\t [\n\t\t\t\t\t\t\t\t\"1321184000365240114236816479208164217123473043443213174227113077515267780427\",\n\t\t\t\t\t\t\t\t\"9588815572669609102065114656724798374518140131116623313758405358680261499082\"\n\t\t\t\t\t\t\t ],\n\t\t\t\t\t\t\t [\n\t\t\t\t\t\t\t\t\"1\",\n\t\t\t\t\t\t\t\t\"0\"\n\t\t\t\t\t\t\t ]\n\t\t\t\t\t\t\t],\n\t\t\t\t\t\t\t\"pi_c\": [\n\t\t\t\t\t\t\t \"20270283025961949711733235392141938263044103713056685530615033679931520723664\",\n\t\t\t\t\t\t\t \"8871872070133185879918010372399365027368084761154313139928014267382902159344\",\n\t\t\t\t\t\t\t \"1\"\n\t\t\t\t\t\t\t],\n\t\t\t\t\t\t\t\"protocol\": \"groth16\",\n\t\t\t\t\t\t\t\"curve\": \"bn128\"\n\t\t\t\t\t\t }",
                    "publicInputs": "[\n\t\t\t\t\t\t\t\"33\"\n\t\t\t\t\t\t ]"
                }
            ]
        }
    ]
}
```
