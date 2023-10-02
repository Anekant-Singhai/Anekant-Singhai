#escapeHtml4 #jsp

This code series is from "Security explained by Harsh Bothra"
vulnerable code:
***
```js
<script>
<%
	String searchTxt = StringEscapeUtils.escape.Html4(request.getParameter("Search"));
%>
document.cookie = 'search=<%searchTxt%>';
</script>

```
***

Looking at how the data is passed : in the searchTxt variable via the "<% %>" code tags usually called jsp or java servlet pages to dynamically generate the servlets using java. We get to know that the variable is taking from the function `escape.Html4` and that function as stated in the docs here:
https://commons.apache.org/proper/commons-lang/javadocs/api-3.1/org/apache/commons/lang3/StringEscapeUtils.html#escapeHtml4(java.lang.String)
It states that this function doesn't filters the single quotations (') so attacker can use them to execute the payloads such as `'+alert(1)+'`
which will look like this:
`document.cookie='search='+alert(1)+'`

## Remedy:
add a filter :
```
String searchTxt = StringEscapeUtils.escapeHtml4(request.getParameter("search")).replace("'","&#39;");
```

