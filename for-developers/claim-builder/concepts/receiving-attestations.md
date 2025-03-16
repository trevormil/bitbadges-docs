# Receiving Attestations

Within the user inputs for both pre-built and custom plugins, we support accepting the "Attestation" type. This allows you to securely receive user selected attestations they have saved or uploaded which may be private via webhooks or plugins. This is only possible with standard in-site claims because the user has to be signed in and select it manually.

You will receive the attestation in the payload of your request via the **key** you configure. The builder should give you a preview.

{% content-ref url="../plugins/creating-a-custom-plugin/alternatives.md" %}
[alternatives.md](../plugins/creating-a-custom-plugin/alternatives.md)
{% endcontent-ref %}

IMPORTANT: Don't trust, verify! You should also note that we simply provide you with the attestation. Treat BitBadges as the middleman. All custom checks like the content of the messages, issuer is correct, etc.

You need to verify the attestations on your side. Verification is done according to the expected format. If you need to check BitBadges core ones (scheme == 'bbs' || scheme == 'standard'), you can use the SDK's **verifyAttestation** function or API **verifyAttestation** route. If it is a third-party upload, see the corresponding documentation.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
