# Managing Your Plugin

### Updating Your Plugin / Version Control

Management of your plugin is done through the developer portal. You may configure different versions of your plugin. New claims will always use the latest **finalized** version. Existing claims will remain on the version they were created with. Unfinalized versions can ONLY be used by you in the claim tester.

We leave updates and version control management up to you. It is your responsibility to keep claims compatible and functioning. If you need to implement a breaking change, you can also consider using the createdAt, lastUpdated, or version fields passed via the context to implement version control and handle it on your end. Or, you can also create a new plugin.