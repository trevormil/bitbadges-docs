# Overview

For gating Discord channels, you will create role-gated channels in the Discord interface and assign the roles to users who meet the valid criteria (badge ownership, etc). We leave this initial setup step up to you.

You have two options for implementation:

1. Use the Discord role assigner claim plugin created by BitBadges. Less code but not as flexible. This is a plugin that can be added into BitBadges claims and attempts to assigns roles during the claim.
2. Self-implement for enhanced customization options.

WARNING: The plugin approach has the following properties:

* One-way set only. Does not support revoking roles. Only adding them.
* Does not support reprompts, rechecking criteria, caching details, etc.
* May not exactly align with claim successes. Claims will only be successful if the role assignment is successful, but you could end up with cases that the plugin (role assignment) may be successful without the claim being successful. For example, a later plugin or the claim action might fail. There is no rollback roles option.

Only use the plugin if you are okay with these properties.
