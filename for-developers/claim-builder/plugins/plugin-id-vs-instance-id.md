# Plugin ID vs Instance ID

You may see the terms pluginId and instanceId around in this documentation or via the API.

**Plugin IDs**

Plugin IDs are constant and specific to a specific plugin type. For example, the password pluginId is "password".&#x20;

**Instance IDs**

Instance IDs are a unique identifier for a specific plugin in your claim. This is to handle duplicates. For example, you may have two password plugins, one with instance Id "abc" and one with "def". This is just used as a unique identifier and can really be anything. If you only have one instance of a specific type, you can name it the same as the pluginId as well.

