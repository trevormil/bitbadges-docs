# Managing Your Plugin

### Updating Your Plugin

We leave updates and version control management up to you. It is your responsibility to keep claims compatible and functioning. Updating can be managed via the developer portal. You can create new versions of the plugin. New claims will always use the latest finalized version. Existing claims will remain on the version they were created with.

If you need to implement a breaking change, you can also consider using the createdAt, lastUpdated, or version fields passed via the context to implement version control and handle it on your end.

Or, you can also create a new plugin.

### Deleting Your Plugin

You can delete your plugin via https://bitbadges.io/developer. Note that this is a soft delete. It removes it from the directory, but all details will still be stored to allow existing claims to still be attempted.
