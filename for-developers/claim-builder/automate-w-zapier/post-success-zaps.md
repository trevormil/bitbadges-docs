# Post-Success Zaps

If you want to implement post-success Zaps, consider using the New Claim Success trigger. This will poll and execute the Zap for every new claim that is completed. You have access to the claiming address / claim number (zero-based) in the response.

<figure><img src="../../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

If you need more information like user socials or custom inputs, consider creating a custom plugin or using a webhook plugin to handle this and trigger the Zap manually via the Webhoks by Zapier plugin.

<figure><img src="../../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>
