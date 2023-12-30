# Sign + Broadcast - bitbadges.io

To broadcast a Msg using this [https://bitbadges.io/dev/broadcast](https://bitbadges.io/dev/broadcast)  user interface, all you have to do is to copy and paste the Msg contents into the text box. You should have the **txn** object from prior pages in this tutorial. Or, you can start with the samples provided and customize from there.

Or, if you are wanting to redirect to this page, you can pass in the stringified URL **txsInfo** query param, which will be auto-populated.  It is expected to be a JSON stringified array of TxInfo\[] as seen below.

```typescript
export interface TxInfo {
  type: string, //'MsgCreateCollection'
  msg: object, //JSON stringified message
}
```

This transaction builder ONLY deals with the Msg contents and not anything about the transaction context (handled by the site behind the scenes). The interface will provide you with default examples. Make sure all tproperties align and no extra properties like account\_number, sequence, etc are pasted. These are handled behind the scenes.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
