I recently came across an intriguing study by liveoverflow, a renowned cybersecurity researcher, that sheds light on an interesting behavior of HTML tags as well as why we want to sanitize the input for xss in client side rather than server side. In his videos, liveoverflow demonstrates how HTML tags can be mutated and manipulated to potentially bypass HTML sanitizers and execute malicious code.

```
https://www.youtube.com/watch?v=HUtkW2gjC8Q

https://www.youtube.com/watch?v=lG7U3fuNw3A
```

Looking at this weird behaviour of html where when we create a tag from number we get this as output:
```
<22>test</22>
```
![[Pasted image 20230715000659.png]]
![[Pasted image 20230714133250.png]]
What if we try adding any tag inside this attribute , will the browser make it work?
![[Pasted image 20230714133546.png]]
Actually it does , the mutation of the tags lead to an execution of our html:
![[Pasted image 20230714133559.png]]

The h1 tag survived the mutation of the html by the browser and due to that the browser rendered it 
so what if we try giving the img tag xss payload , will it show any sign of xss? , let's try:
![[Pasted image 20230714134257.png]]
Indeed it does the payload:
```
<22 foo="bar<img src=x onerror=alert(1)>">test</22>
```

Look how the img tag is in between the value of the `  foo ` attribute i.e `"bar <the payload>"`
Let's change the `22` tag to a `div` tag:
![[Pasted image 20230714134608.png]]

Doing so results in a perfectly normal execution of the html without any mutatuion
![[Pasted image 20230714134703.png]]

The html sanitizer parses the html and sees a harmless `22` tag making it , so can we really bypass the html sanitizer?:

```html
<42 asd="<img src=x onerror=alert(1)//">x</42>
```


NO , not really , due to the fact that when the , and also the good html sanitizers work on the *client side* rather that sanitizing the input in the server side due to that fact that client side sanitization.
But 
## Why do we want to sanitize the data client side?
look at these two codes:
```
<div><script title="</div>">

<script><div title="</script>">
```
and how they render by the web browsers:
![[Pasted image 20230715002008.png]]
The `first example` is a div tag with script tag inside , while the browser thinks the title string as an attribute string and closes the script tag and after that closing the div tag

`other example` is of a script tag containing a div tag but the script tag must contain javascript code  and the browser closes the script tag but no div tag to be closed?? while on first one we see that browser fixed the code but not in second one 
![[Pasted image 20230717131838.png]]
like this , so what happened :
there are two different types of parsers that come in picture , 
now what are parsers:
***
Parsers are fundamental components of software applications that play a crucial role in processing and interpreting data or code written in a specific syntax or format. They are responsible for *breaking down* the input data into a structured representation, making it easier for the application to understand and work with the information.
***
so the browsers have these HTML and JAVASCRIPT parsers , in the first case the content was in a div tag and browsers know that a div tag contains HTML , so it used an HTML parser and seperated the code and fixed it as HTML parsers recognizes these elements but in the second case , browsers know that script tag contains the javascript so it switched to the javascript parser that didn't recognises the div element so it felt no need to close it this behaviour continues until we have closed the script tag , this behaviour is abused in an advanced class of XSS called mutation XSS

That's why we need *Client Side Sanitation*
What happens , even if the data is sanitized server side , the browser's weird behaviour leads to some mutation , so we let the browser parse the html and then we sanitize it on client side but it requires quite a skill to sanitize so that it doesn't execute scripts and also render the html required.
One of the example is by creating a template tag:
First see this behaviour of this div tag:
```
div = document.createElement("div")
div.innerHTML = "<img src=x onerror=alert(1)>"
```
![[Pasted image 20230717133946.png]]

Now let's try the same with the template element
![[Pasted image 20230717134159.png]]
NO alert and also:
![[Pasted image 20230717134306.png]]

***
The code `template.content.children[0]` is used to access the first child element of the `template` element's content. Let's break down the code step by step:

1. `template`: This refers to a HTML `<template>` element. The `<template>` element is a mechanism in HTML that allows you to define reusable content that can be cloned and used at multiple places in the document without being rendered directly. It acts as a container for a template that can be dynamically inserted into the document.
    
2. `template.content`: The `.content` property of a `<template>` element gives access to the content of the template. It provides a DocumentFragment object that represents the content of the `<template>`. The DocumentFragment is an in-memory DOM node that can hold a collection of other DOM nodes without being part of the main DOM tree.
    
3. `children`: The `.children` property of a DOM node gives a live HTMLCollection of its child elements. It contains all the immediate child elements of the element on which it is accessed.
***
But why it didn't execute?
Here in this article it says:
`https://javascript.info/template-element`
![[Pasted image 20230717135015.png]]
![[Pasted image 20230717135110.png]]

It checks the right syntax and it is "Out-Of-Document" meaning out of DOM and thus it doesn't affect the DOM
The browser parsed it safely and this line tops it all:
![[Pasted image 20230717135202.png]]
The content then we can insert it into the DOM as we like so , whenever an attribute containing payloads like `onerror=<script>` , we can remove the safely and then insert into DOM:
![[Pasted image 20230717135515.png]]
Also in the documentation of the parse errors in the html :
[[https://html.spec.whatwg.org/multipage/parsing.html#parsing-with-a-known-character-encoding]]

we can see that:
![[Pasted image 20230714141225.png]]
and in one of these errors is the error:
![[Pasted image 20230714141451.png]]
where it clearly says that the starting tag should be an ascii-alpha character i.e [a-z | A-Z]
and also gives the same example we saw above we can't start with the numeric tag but we can add numbers after
It also says how the browser will parse it into then:
![[Pasted image 20230714142141.png]]
So it was never an html "tag" but rather an html text and the payload inside it was not attribute but the tag itself , that was being executed
