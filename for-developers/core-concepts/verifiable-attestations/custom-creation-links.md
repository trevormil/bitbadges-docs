# Custom Creation Links

To make it convenient for you to redirect users and auto-create attestations, you can create unique create / update links with the details auto-filled out.

There are two query parameters that can be passed.

**attestationId:** ID of the existing attestation. Only needed for updates. For create links, leave blank.

**toSet:** A JSON-stringified `CreateAttestationPayload`object that we will set. Not all properties are required. We will only overwrite the ones provided. We refer you to the previous page for configuration.

```
https://bitbadges.io/attestations/create?toSet={}&attestationId=...
```

For example

<pre class="language-typescript"><code class="lang-typescript"><strong>const content: Partial&#x3C;CreateAttestationPayload> = {
</strong>  "messageFormat": "plaintext",
  "type": "credential",
  "scheme": "standard",
  "name": "Custom Name",
  "image": "ipfs://QmNytJNN44stkMndshtdfcCW2mzaCm6A23maiKaQvUqoj8",
  "description": "",
  "attestationMessages": [
    "super secret message"
  ]
}

const url = 'https://bitbadges.io/attestations/create?toSet=' + JSON.stringify(content)
</code></pre>

```
https://bitbadges.io/attestations/create?toSet={ "messageFormat": "plaintext", "type": "credential", "scheme": "standard", "name": "Custom Name", "image": "ipfs://QmNytJNN44stkMndshtdfcCW2mzaCm6A23maiKaQvUqoj8", "description": "gfdsgxdfgsdf", "attestationMessages": [ "super secret message" ] }
```
