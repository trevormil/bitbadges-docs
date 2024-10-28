# WITNESS Proofs

Create WITNESS proofs and custom upload them to BitBadges via our attestation interface.

**Overview**

Anyone can use Witness to create verifiable [provenance](https://docs.witness.co/concepts/provenance) and issue [digital ownership](https://docs.witness.co/concepts/provenance) for any data without the upfront friction or cost of blockchains. This allows developers to build traditionally scalable applications that benefit from Web3 incentives such as:

* Tokenization: NFTs, tokens, points
* Universal Verifiability: attestations, logs, timestamps
* Financialization: mint fees, referral fees, smart contracts composability

We refer you to [their docs](https://docs.witness.co/) for further implementation details.&#x20;

**Scheme Name**

The **scheme** property should be:

```typescriptreact
scheme: "witness-proof"
```

You can also set the originalProvider:

```
"originalProvider": "Witness"
```

**Message Interface**

```
"messageFormat": "json"
```

We expect the following interface for message content (stringified JSON) to be compatible with our UI.&#x20;

```typescript
interface WitnessProof {
  proof: {
    leafHash: string;
    leafIndex: bigint;
    leftHashes?: string[];
    rightHashes?: string[];
    targetRootHash: string;
  };
  chainId?: string;
  preimage?: string;
  timestamp?: number;
  txHash?: string;
}
```

```json
{
  "preimage": "Testing witness proofs",
  "proof": {
    "leafIndex": "30800033",
    "leafHash": "0x570751d4efee586ef4baa79c336778de08b019b69f182e68561ba2b44bce2d7c",
    "leftHashes": [
      "0xfd663a79b001aa5bcd55ce32aae934fc69281bc19b83f287236b572acaf57962",
      ...
    ],
    "rightHashes": [
      "0x3c6710710882171f7a3b07172d7637cfbf385e46fcfe8560eb2822410c4eca8d",
       ...
    ],
    "targetRootHash": "0xf34cf9b13d69ee9af5110841baf2a9b294ff13926d4f60e13222edaa7243de68"
  },
  "timestamp": 10000000, //Unix milli timestamp
  "chainId": "8453", //Defaults to this for "Base"
  "txHash": "0x842f3a5ac844ee222865cb03ea28c04f0eb65a4896b83d40c5cb4900cb7ffcc1"
}
```

Note: When stringified, it would be like

```json
"attestationMessages": [
    "{\n  \"preimage\": \"Testing witness proofs\",\n  \"proof\": {\n    \"leafIndex\": \"30800033\",\n    \"leafHash\": \"0x570751d4efee586ef4baa79c336778de08b019b69f182e68561ba2b44bce2d7c\",\n    \"leftHashes\": [\n      \"0xfd663a79b001aa5bcd55ce32aae934fc69281bc19b83f287236b572acaf57962\",\n      \"0x44b18b19f7da0a75ea66af7fa9cdd3e5f376749ff3c201beaa867abfbd99270d\",\n      \"0xb52cfafbe335afc9eb38b74b1b9186c5426807985b99ecc568c338ed22f6d739\",\n      \"0x027cd88daedfbbf04cea7abd17b70504bdfb56d27541e654f5762d44f06e3cce\",\n      \"0xa0f9df4a46928425bb01b40bca6ead558358c62f4b692392cc4e8c76fe2c1fb1\",\n      \"0x53a276dac9002ecb766cb865198e912baf114fd3c95079f19ef2d9abe3a078f4\",\n      \"0xe1af8f9e39e7b57a7eee0d3f47cf111a3291c29ce757d6d542aca8d590bb1013\",\n      \"0xf1250deb418ab6f57d4482e1243cd8aadd021afd546a18a0a067757a0ad6198d\",\n      \"0x1351fef7c4972ebb9ac5b0691c411cf40e1773fbb8168e664bb0b13cffcfa07d\",\n      \"0x2d7002b8f80a5ff993c605298a6453293bc1bc827334fb9d46d6030e01992808\",\n      \"0x000f731409fed7667e5d01e2cf9d8d0da96556bbb2fb7e3b3b8e1028dab70d62\",\n      \"0x6b610a565e82e7eef56d58e66867b32cbd09865d09d991d0835e24786757214e\",\n      \"0xd73a3a8609a8b46cf508d84e040e785ad3cb7cdbc63e18fdf72b4d663f5e0a5f\",\n      \"0xedcbfd0e6a5172e1f0639e9f7e45cdd0bfdc7d05f27cb517deeb17c382b97f6d\"\n    ],\n    \"rightHashes\": [\n      \"0x3c6710710882171f7a3b07172d7637cfbf385e46fcfe8560eb2822410c4eca8d\",\n      \"0x4b3e0a2b6e91e1b788833344c3a1ce8380a5f444156cd314727348fc740ca427\",\n      \"0x8cb377e84af2f9415442daf363253aad4624b4df6bb3f2ce6c5c56d3bcfb2c49\",\n      \"0xdae86492eda6c7ea786ca598c978dade5d0939f28e0fc90f77a0289e1a8628c0\",\n      \"0xdd8c18abd775815100d4999419a2a3a718efcc35f1ffcd150f0068f829ad634a\",\n      \"0x94f2dd188c291f98615941697f93e7c17ca97083993d6bf14a4e5376a3d0c1b0\",\n      \"0x19ce36b7f94c5158073e825d622a58b455f65bc167cc35a2f758cc6d0e21fafb\",\n      \"0x1d8eb78409c5c2699dea20d3b4090afeedf8c3bfd911f2a7b695234d24946ec6\",\n      \"0x9667094385f73adf330afe89f20ee16a1d63f718c17b34eba93e45dfd003deb5\"\n    ],\n    \"targetRootHash\": \"0xf34cf9b13d69ee9af5110841baf2a9b294ff13926d4f60e13222edaa7243de68\"\n  },\n  \"timestamp\": 10000000,\n  \"chainId\": \"8453\",\n  \"txHash\": \"0x842f3a5ac844ee222865cb03ea28c04f0eb65a4896b83d40c5cb4900cb7ffcc1\"\n}"
]
```

\
**Other Fields**

There is no need for **proofOfIssuance** or **dataIntegrityProof** (can be left blank) since everything is included in the message content already.

Everything else is customizable as-is.

**Full Example Attestation**

```json
{
  "updateHistory": [
    {
      "txHash": "",
      "block": "188097",
      "blockTimestamp": "1730123159671",
      "timestamp": "1730123162836"
    }
  ],
  "_docId": "009a55376a52b5df74e15f47397052b3abbf74aee60d8f7399357d566c35e56a",
  "_id": "5d044ba2f73444dd747cd11a",
  "createdBy": "bb1dpxqmz2h835lq88qulvytnq6mpu4x5ylwhmhh9",
  "messageFormat": "json",
  "attestationId": "009a55376a52b5df74e15f47397052b3abbf74aee60d8f7399357d566c35e56a",
  "inviteCode": "b969dd52bf7a9e8ce0811eb214e8249a88cff064b1e61221a3f4c1fda446e423",
  "type": "credential",
  "scheme": "witness-proof",
  "dataIntegrityProof": {
    "signer": "",
    "signature": "",
    "publicKey": ""
  },
  "holders": [],
  "name": "dfgsdfg",
  "image": "ipfs://QmNytJNN44stkMndshtdfcCW2mzaCm6A23maiKaQvUqoj8",
  "description": "gfdsgxdfgsdf",
  "proofOfIssuance": {
    "signature": "",
    "signer": "",
    "message": ""
  },
  "anchors": [],
  "attestationMessages": [
    "{\n  \"preimage\": \"Testing witness proofs\",\n  \"proof\": {\n    \"leafIndex\": \"30800033\",\n    \"leafHash\": \"0x570751d4efee586ef4baa79c336778de08b019b69f182e68561ba2b44bce2d7c\",\n    \"leftHashes\": [\n      \"0xfd663a79b001aa5bcd55ce32aae934fc69281bc19b83f287236b572acaf57962\",\n      \"0x44b18b19f7da0a75ea66af7fa9cdd3e5f376749ff3c201beaa867abfbd99270d\",\n      \"0xb52cfafbe335afc9eb38b74b1b9186c5426807985b99ecc568c338ed22f6d739\",\n      \"0x027cd88daedfbbf04cea7abd17b70504bdfb56d27541e654f5762d44f06e3cce\",\n      \"0xa0f9df4a46928425bb01b40bca6ead558358c62f4b692392cc4e8c76fe2c1fb1\",\n      \"0x53a276dac9002ecb766cb865198e912baf114fd3c95079f19ef2d9abe3a078f4\",\n      \"0xe1af8f9e39e7b57a7eee0d3f47cf111a3291c29ce757d6d542aca8d590bb1013\",\n      \"0xf1250deb418ab6f57d4482e1243cd8aadd021afd546a18a0a067757a0ad6198d\",\n      \"0x1351fef7c4972ebb9ac5b0691c411cf40e1773fbb8168e664bb0b13cffcfa07d\",\n      \"0x2d7002b8f80a5ff993c605298a6453293bc1bc827334fb9d46d6030e01992808\",\n      \"0x000f731409fed7667e5d01e2cf9d8d0da96556bbb2fb7e3b3b8e1028dab70d62\",\n      \"0x6b610a565e82e7eef56d58e66867b32cbd09865d09d991d0835e24786757214e\",\n      \"0xd73a3a8609a8b46cf508d84e040e785ad3cb7cdbc63e18fdf72b4d663f5e0a5f\",\n      \"0xedcbfd0e6a5172e1f0639e9f7e45cdd0bfdc7d05f27cb517deeb17c382b97f6d\"\n    ],\n    \"rightHashes\": [\n      \"0x3c6710710882171f7a3b07172d7637cfbf385e46fcfe8560eb2822410c4eca8d\",\n      \"0x4b3e0a2b6e91e1b788833344c3a1ce8380a5f444156cd314727348fc740ca427\",\n      \"0x8cb377e84af2f9415442daf363253aad4624b4df6bb3f2ce6c5c56d3bcfb2c49\",\n      \"0xdae86492eda6c7ea786ca598c978dade5d0939f28e0fc90f77a0289e1a8628c0\",\n      \"0xdd8c18abd775815100d4999419a2a3a718efcc35f1ffcd150f0068f829ad634a\",\n      \"0x94f2dd188c291f98615941697f93e7c17ca97083993d6bf14a4e5376a3d0c1b0\",\n      \"0x19ce36b7f94c5158073e825d622a58b455f65bc167cc35a2f758cc6d0e21fafb\",\n      \"0x1d8eb78409c5c2699dea20d3b4090afeedf8c3bfd911f2a7b695234d24946ec6\",\n      \"0x9667094385f73adf330afe89f20ee16a1d63f718c17b34eba93e45dfd003deb5\"\n    ],\n    \"targetRootHash\": \"0xf34cf9b13d69ee9af5110841baf2a9b294ff13926d4f60e13222edaa7243de68\"\n  },\n  \"timestamp\": 10000000,\n  \"chainId\": \"8453\",\n  \"txHash\": \"0x842f3a5ac844ee222865cb03ea28c04f0eb65a4896b83d40c5cb4900cb7ffcc1\"\n}"
  ],
  "createdAt": "1730123162836",
  "originalProvider": "Witness"
}

```
