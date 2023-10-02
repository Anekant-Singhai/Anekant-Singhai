
## second challenge: Adobe
![[Pasted image 20230725131113.png]]


## Third challenge: JSON
***
```
function escape(s) {
  s = JSON.stringify(s);
  return '<script>console.log(' + s + ');</script>';
}
```
***
The **`JSON.stringify()`** static method converts a JavaScript value to a JSON string, optionally replacing values if a replacer function is specified or optionally including only the specified properties if a replacer array is specified.
meaning if the value is specified just convert it to json
if something to replace is specified -> replace that thing then convert to json
```
JSON.stringify(value)
JSON.stringify(value, replacer)
JSON.stringify(value, replacer, space)
```

So the function converts our input to json

