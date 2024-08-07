# Protocols

### What Are Protocols?

Protocols allow you to group certain data from different users for a specific purpose. Think of it as a way to organize and manage similar activities or goals.

#### Example: Preferences Protocol

A preferences protocol might allow you to set personal preferences that can be used across different applications. It's like setting up your preferences once and having all your apps respect those settings.

Imagine you prefer using apps in dark mode. With a Preferences Protocol, you can set this preference once, and any app that supports this protocol will automatically switch to dark mode for you.

#### Example: BitBadges Follow Protocol

The **BitBadges Follow Protocol** lets users create "follow badge collections." If you own a follow badge from someone's collection, it means you are following them.

Different users can customize how they manage their follow badges. Some might choose to store them off-chain (not on the blockchain), while others prefer the security and transparency of on-chain storage.

Each user has control over their collection, making management easier and more flexible.

### How Protocols Work

Protocols use a subset of the map interface (a way of organizing data). They work with specific types of messages and use unique keys for querying.

#### Example Setup

Users can set up their collections for different protocols. Here’s how it might look for a specific address:

```json
// 0x123456...789 set the following protocol values
{
    "BitBadges Follow Protocol": 12, // Collection ID 12 is used for my follows
    "Experiences Protocol": 13       // Collection ID 13 is used for my experiences
}
```

This information is stored on-chain for reliability and accessibility.
