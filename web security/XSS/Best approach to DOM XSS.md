First of we need to understand the nature of this vulnerability and how it occurs , then we proceed to look forward to how to detect and exploit it

## What is DOM
DOM refers to the "structure" of the HTML in which manner it is parsed by the browser in order to render the page as desired by the developer.
The DOM is the tree , the actual structure of the 
website that we see when we press `f12` that hierarchial structure of the elements , tags and content inside them. 
like this:
![[Pasted image 20230717181956.png]]
That structure is treated as a document which can be changed with the help of javascript to create the dynamic website or as to say change the structure and looks as per the actions by the user or as developer needs

Mozilla web docs say:
![[Pasted image 20230717181258.png]]
A programming Interface eh? , well they are not wrong , 
DOM stands for Document Object Model. It is a programming interface that allows us to create, change, or remove elements from a website document. DOM manipulation is when you use JavaScript to add, remove, and modify elements of a website. But how can we manipulate something that is there on the browser with javascript?
like this:
We can set whatever data we want inside any element of the DOM element we want:
![[Pasted image 20230717182650.png]]
![[Pasted image 20230717182738.png]]
Now this javascript is set by developers to change the DOM heavily. But how this can be dangerous?

## DOM Xss
***
>OWASP says:
DOM Based [XSS](https://owasp.org/www-community/attacks/XSS "wikilink") (or as it is called in some texts, “type-0 XSS”) is an XSS attack wherein the attack payload is executed as a result of modifying the DOM “environment” in the victim’s browser used by the original client side script, so that the client side code runs in an “unexpected” manner. That is, the page itself (the HTTP response that is) does not change, but the client side code contained in the page executes differently due to the malicious modifications that have occurred in the DOM environment.
>
>This is in contrast to other XSS attacks ([stored or reflected](https://owasp.org/www-community/attacks/XSS#Stored_and_Reflected_XSS_Attacks "wikilink")), wherein the attack payload is placed in the response page (due to a server side flaw).
***

Okay so we can change the content in the browser by tricking in the javascript to embed the malicious code to the DOM , then browser executing it
The nature of this vulnerability is client side as the javascript that works on client side is responsible for this behaviour due to lack of sanitization and improper use to secure-coding practices are main root of this archilies heel

## Steps to Identify and Exploit DOM XSS

## Step 1 
### Identify the SINK

according to portswigger:
DOM-based XSS vulnerabilities usually arise when JavaScript takes data from an attacker-controllable source, such as the URL, and passes it to a sink that supports dynamic code execution, such as `eval()` or `innerHTML`. This enables attackers to execute malicious JavaScript, which typically allows them to hijack other users' accounts.

Now what is this `sink`?
A sink is **a potentially dangerous JavaScript function or DOM object that can cause undesirable effects if attacker-controlled data is passed to it**.
like this in the lab here:
![[Pasted image 20230717185801.png]]
document.write is a function that writes any given value to the "document" aka DOM  , we have a search funcitonality , when we search something and look at the code:
![[Pasted image 20230717190229.png]]
we not only get the single picture filtered but the code says:
![[Pasted image 20230717190603.png]]
line 1 : take the query entered by the user and append it into the url like this:
![[Pasted image 20230717190709.png]]
line 2: extract the value of the "search" query parameter from the URL of the current web page and assign it to the query variable
***
1. `window.location.search`: This retrieves the query string from the URL of the current web page. The query string is everything that follows the "?" in the URL.
2. `new URLSearchParams(window.location.search)`: This creates a new URLSearchParams object, which allows easy access to the query parameters in the URL.
3. `.get('search')`: This method of the URLSearchParams object is used to retrieve the value of the "search" query parameter. If the "search" parameter exists in the URL, it returns its value; otherwise, it returns null.
***
line 3 and 4: if the value of query exists: then search the query and display the post regarding it

So the `Sink` here from above definition is the function `document.write` function that is taking our value

we can also check where our value is going is via pressing `f12` and in the console we execute this code:
`location.search`
![[Pasted image 20230717191606.png]]
## Step 2:
### See how our value is being displayed:
When we look at the DOM changed by the js above we see that is where the query is :
![[Pasted image 20230717191332.png]]
`src="/resources/images/tracker.gif?searchTerms=game"`
so always ask yourself : can we escape the tag here and inject our own tags?
like
`searchTerms=game"/><script>alert(1)</script>`
so can we pollute the sink?
let's try
```
"/><script>alert(1)</script>
```
## Step 3
### Pollution of sink 
and we get the alert
![[Pasted image 20230717192126.png]]

now we need to consider that some javascript code can be containing filters so we need to bypass that too , I'll cover that in next blog


# Example 2
Google XSS game level 3:
![[Pasted image 20230717194944.png]]
When we look at the images -> we see that the url in the iframe changes:
https://xss-game.appspot.com/level3/frame#1
https://xss-game.appspot.com/level3/frame#2
https://xss-game.appspot.com/level3/frame#3
on looking the source code: 
![[Pasted image 20230717195202.png]]
## Identify the sink:
where the image numbers are going is:
![[Pasted image 20230717195337.png]]
## See how our data is being rendered:
see when I clicked on 2:
![[Pasted image 20230717195610.png]]
![[Pasted image 20230717195709.png]]
Snippet 1 says take the number and add to the url and embed in dom like:
![[Pasted image 20230717195831.png]]
snippet 2 and 3 are just adding the functionality saying then say which tab is active according to the number chosen and 3 says to tell the DOM parent of the tag that it's changed 
## Pollute the sink:
`<img src="/static/level3/cloud1.jpeg>"`
try making the payload by copying the excat render and changing it bu adding your value
`<img src="/static/level3/cloud1"><script>alert(1)</script><!--.jpeg>"`
and we get the payload:
`"><script>alert(1)</script><!--`
![[Pasted image 20230717200254.png]]
NOPE doesn't work as the quotes we added to escape aren't being recognised , so what to do now:
we now approach to find different type of payload , rather than escaping the function we add onerror :
`3' onerror='alert();//`
This brother here explaied why it works:
https://medium.com/bugdecoder/google-xss-game-walkthrough-70d801dd922
***
**Why does it work:** The page’s source code shows that it gets the URL fragment and passes to a _choosetab_ function.

window.onload = function() {chooseTab(**unescape(self.location.hash.substr(1))** || "1");}
***
![[Pasted image 20230717200655.png]]