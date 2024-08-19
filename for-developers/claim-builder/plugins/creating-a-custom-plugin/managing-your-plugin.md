# Managing Your Plugin

### Updating Your Plugin

We leave updates and version control management up to you. It is your responsibility to keep claims compatible and functioning. Updating can be managed via the developer portal.

If you need to implement a breaking change, consider using the createdAt, lastUpdated, or version fields passed via the context to implement version control and handle it on your end.

The version number will increment by 1 every change you make to the plugin.

### Deleting Your Plugin

You can delete your plugin via https://bitbadges.io/developer. Note that this is a soft delete. It removes it from the directory, but all details will still be stored to allow existing claims to still be attempted.
