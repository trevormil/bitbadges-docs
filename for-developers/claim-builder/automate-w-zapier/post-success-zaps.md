# Post-Success Zaps

If you want to implement post-success Zaps, consider using the New Claim Success trigger. This will poll and execute the Zap for every new claim that is completed. You have access to the claiming address / claim number (zero-based) in the response.

<figure><img src="../../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

Note the only available information from this is the user address and claim number. If you need more information like socials or custom inputs, consider creating a custom plugin or using one of the custom webhook plugins.
