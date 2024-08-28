# Overview

For gating Discord channels, you will create role-gated channels in the Discord interface and assign the roles to users who meet the valid criteria (badge ownership, etc). We leave this initial setup step up to you.

You have two options for implementation:

1. Use the Discord role assigner claim plugin created by BitBadges. Less code but not as flexible. This is a plugin that can be added into BitBadges claims and attempts to assigns roles during the claim.
2. Self-implement for enhanced customization options.

The plugin approach has the following properties:

* No code required on your end.
* One-way set only. Does not support revoking roles. Only adding them.
* Does not support reprompts, rechecking criteria, caching details, etc.
