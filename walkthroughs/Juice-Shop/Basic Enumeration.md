Looking at the source code , we can see that the application runs on the angular js and so it must contain the angular's vulnerability also:
![[Pasted image 20230715153255.png]]
### Package.json
It is an important file telling us the versions and libraries used for this project , looks like they used the node modules like express , database : SQLite , node engine 14-19 , typescript - 4.6.0
and much more , we can search these versions for the vulnerability in them

### Routes folder:
This folder helps us to gain the knowledge of the handler functions of the endpoints the web-developer configured to browse
These routes help the developers to determine what info to be displayed in every page , and define the appropriate URL to return those resources. So we get those endpoints also which are configured 

The diagram below is provided as a reminder of the main flow of data and things that need to be implemented when handling an HTTP request/response. In addition to the views and routes the diagram shows "controllers" — functions that separate out the code to route requests from the code that actually processes requests.
![[Pasted image 20230715154822.png]]
like we get this easter-egg hidden route is there somewhere via this: and some important routes like payment and orders and all
![[Pasted image 20230715155922.png]]

## Hidden Score Board:
There is this famous score board of juice-shop , let's try to find it , I have 2 ways to find it:
### Way 1:
Via source code:
having said that this score board must have a front-end , let's start from there:  the frontend folder
we go to src for source code for that page , in that we go to appications :
frontend -> src -> apps :
and we get it:
![[Pasted image 20230715161904.png]]
we can also find it via dev-tools:

when we register , we get this in our burp exposing the api endpoint as well as the scoreboard too
![[Pasted image 20230715164656.png]]