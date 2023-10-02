the site has the flag characters scattered all over.... very annoying , and these are the flag character holders in the source code ,
let me tell you a power of javascript :
DOM manipulation
![[Pasted image 20230624205924.png]]

we can extract these via this span id js:
```
// Get all the letter elements
const letterElements = document.querySelectorAll('.word span');

// Extract the letters
let flag = ''; // declare an empty string
letterElements.forEach(element => {
  flag += element.textContent;
});

// Output the extracted flag
console.log(flag);

```
![[Pasted image 20230624210557.png]]

