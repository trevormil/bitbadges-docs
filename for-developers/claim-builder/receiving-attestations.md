# Receiving Attestations

Within the user inputs for both pre-built and custom plugins, we support accepting the "Attestation" type. This allows you to securely receive user selected attestations they have saved or uploaded which may be private. This is only possible with standard in-site claims because the user has to be signed in and select it manually.

{% content-ref url="plugins/creating-a-custom-plugin/alternatives.md" %}
[alternatives.md](plugins/creating-a-custom-plugin/alternatives.md)
{% endcontent-ref %}

IMPORTANT: While we do our best to maintain the well-formedness and verification of attestations, attestations might be custom uploaded, be an unsupported schema type, etc, or we might even have a bug. As best practice, you should always verify on your end (server-side). Don't trust, verify! If you need to check BitBadges core ones (scheme == 'bbs' || scheme == 'standard'), you should use the SDK's **verifyAttestation** function. If it is a third-party upload, see the corresponding documentation.&#x20;

You should also note that we simply provide you with the attestation. Treat BitBadges as the middleman. All custom checks like the content of the messages, issuer is correct, etc.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
