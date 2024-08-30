# 🕵️ Authentication with Badges

Authentication is a powerful use case for badges. For example, you can grant access to users who own a specific membership badge while denying access to those who don't. This authentication can be implemented in various settings, including in-person and digital environments.

For developers interested in implementing these features, please refer to our detailed guide:

{% content-ref url="../for-developers/authenticating-with-bitbadges/" %}
[authenticating-with-bitbadges](../for-developers/authenticating-with-bitbadges/)
{% endcontent-ref %}

## Sign In With BitBadges

In digital environments, you can authenticate users and verify badge ownership to enable features like badge-gated website access.

Sign In with BitBadges provides a way for any website to authenticate users while outsourcing all logic to a BitBadges popup. This approach is similar to "Sign In with Google" or other major tech companies.

<figure><img src="../.gitbook/assets/image (50).png" alt=""><figcaption>Sign In With BitBadges Interface</figcaption></figure>

### Integrated Authentication Checks

BitBadges can tap into a wide range of integrations to perform various authentication checks. This flexibility allows for complex and diverse authentication scenarios. Some of the supported integrations include:

1. **Badge Ownership Checks**: Verify if a user owns specific badges or collections.

2. **Discord Server Checks**: Confirm a user's membership or role in a particular Discord server.

3. **Token Holdings**: Check if a user holds a certain amount of specific tokens across various blockchains.

4. **NFT Ownership**: Verify ownership of specific NFTs or collections.

5. **On-chain Activity**: Authenticate based on specific on-chain actions or transactions.

6. **Social Media Verification**: Confirm a user's identity or following on platforms like Twitter.

These integrations allow for highly customizable authentication flows.

## QR Codes

QR codes offer a flexible solution for verification when you need to pre-generate signatures for later use. This is particularly useful in scenarios where users might not have immediate access to their digital wallets.

Use cases for QR code authentication include:

-   Presenting a digital ticket at a real-world event gate
-   Verifying membership at physical locations
-   Providing quick access to badge-gated content in various settings

QR codes in the BitBadges system can be configured in two main ways:

Users have the flexibility to export and save these QR codes using multiple methods, accommodating various preferences and use cases.

### Customizing QR Code Behavior

Depending on the provider's requirements, QR codes can be set up with different behaviors:

-   Expiration dates
-   Usage limits
-   Specific authentication requirements (e.g., must be scanned at a particular location)
-   Linked to specific badges or collections

By leveraging these authentication tools and integrations, BitBadges provides a robust and flexible system for verifying user identities, permissions, and badge ownership across a wide range of digital and physical scenarios.
