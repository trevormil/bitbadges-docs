# ℹ Custom Address Collection Surveys

Pre-Readings: [Distribution](../overview/how-it-works/distribution.md)

The BitBadges site natively allows you to create a address collection survey where users can add their address to a list for you to view. This is useful for collecting addresses at an in-person event. See the pre-reading for more information.

However, the native integration only supports storing the addresses as a list on the BitBadges servers. Sometimes, you may want something more customizable than that. For example, you may want to send a claim alert every time a user enters an address or update your private database.

To facilitate this, we allow you to pass in the following URL query params to https://bitbadges.io/addresscollector.&#x20;

```typoscript
const { description, callbackRequired } = router.query;
```

```typescript
if (window.opener && callbackRequired) {
    window.opener.postMessage({ type: 'address', address: selectedUser }, "*");
}
```

description is simply a helper message that will be displayed to the user.&#x20;

callbackRequired being a truthy vlaue means that we will send a window message back to the site that directed the user here with the inputted address specified.&#x20;

For example, on your site, you may direct them to the /addresscollector site via the first coede block and listen for messages as shown in the second code block.

```typescript
const childWindow = window.open(url, '_blank', 'width=500,height=600');

// You can further customize the child window as needed
childWindow?.focus();
```

<pre class="language-typescript"><code class="lang-typescript"><strong>const handleChildWindowMessage = (event: MessageEvent) => {
</strong>  if (event.origin === FRONTEND_URL) {
    const dataFromChildWindow = event.data;

    // Check if the message contains the QR code data
    if (dataFromChildWindow.address) {
      //You now have access to the entered address
      //TODO: Implement your custom logic
    }
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
</code></pre>

