# Sign + Broadcast - bitbadges.io

**Option 1: Copy / Paste**

To broadcast a Msg using this [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast) user interface, all you have to do is to copy and paste the Msg contents into the text box. You should have the **txn** object from prior pages in this tutorial. Or, you can start with the samples provided and customize from there.

**Option 2: Redirect**

Or, if you are wanting to redirect to this page, you can pass in the stringified URL **txsInfo** query param, which will be auto-populated. It is expected to be a JSON stringified TxInfo\[].

```typescript
export interface TxInfo {
  type: string, //'MsgCreateCollection'
  msg: object, //JSON stringified message
}
```

This transaction builder ONLY deals with the Msg contents and not anything about the transaction context (handled by the site behind the scenes). The interface will provide you with default examples. Make sure all properties align and no extra properties like account\_number, sequence, etc are pasted. These are handled behind the scenes.

If you open via a popup, such as below, it will pass back the txHash via a window callback and auto-close upon success.

```typescript
import { proto } from 'bitbadgesjs-sdk';

const MsgCreateProtocol = proto.protocols.MsgCreateProtocol;
const msgCreateProtocol = new MsgCreateProtocol({
  "name": "Test",
  "uri": "https://www.youtube.com/watch?v=5qap5aO4i9A",
  "customData": "Test",
  "isFrozen": false,
  "creator": chain.bitbadgesAddress
})
const url = 'https://bitbadges.io/dev/broadcast?txsInfo=[{ "type": "MsgCreateProtocol", "msg": ' + msgCreateProtocol.toJsonString() + ' }]';
const openedWindow = window.open(url, '_blank', 'location=yes,height=570,width=520,scrollbars=yes,status=yes');

setLoading(true);
// You can further customize the child window as needed
openedWindow?.focus();

//set listener for when the child window closes
const timer = setInterval(() => {
  if (openedWindow?.closed) {
    clearInterval(timer);
    setLoading(false);
  }
}, 1000);
```

<pre class="language-typescript"><code class="lang-typescript"><strong>// bitbadges.io code
</strong><strong>if (window.opener) {
</strong>    window.opener.postMessage({ type: 'txSuccess', txHash: txHash }, '*');
    window.close();
}
</code></pre>

```typescript
//https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage
const FRONTEND_URL = 'https://bitbadges.io';
const handleChildWindowMessage = async (event: MessageEvent) => {

  if (event.origin === FRONTEND_URL) {

    if (!event.source) {
      throw new Error('Event source is null');
    }

    const txHash = event.data.txHash;
    if (!txHash) {
      //To avoid the listening to self events if we are actually on bitbadges.io and just an overall quality check
      return
    }


    setLoading(false);
  }
};

// Add a listener to handle messages from the child window
useEffect(() => {
  window.addEventListener('message', handleChildWindowMessage);

  // Cleanup the listener when the component unmounts
  return () => {
    window.removeEventListener('message', handleChildWindowMessage);
  };
}, []);
```

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
