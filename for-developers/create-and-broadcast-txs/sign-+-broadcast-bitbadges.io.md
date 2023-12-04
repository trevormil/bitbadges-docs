# Sign + Broadcast - bitbadges.io

Currently,  [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) only supports single Msgs and only the x/badges module Msgs. Expanding this functionality is in our backlog, but let us know if you need specific functionality that is not supported.

To broadcast a Msg using this user interface, all you have to do is to copy and paste the Msg contents into the text box. You should have the **txn** object from prior pages in this tutorial.

Or, if you are wanting to redirect to this page, you can pass in the stringified msg via **txMsg** and the type via **txType** in the URL query params. The params will be auto-populated.&#x20;

You should only paste the Msg contents and not anything about the transaction context. This will be the object at **txn.jsonToSign.msg0.** The interface will provide you with default examples. Make sure all properties align and no extra properties like account\_number, sequence, etc are pasted. These are handled behind the scenes

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
