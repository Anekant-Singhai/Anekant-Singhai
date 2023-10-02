# Types of API:

## Rest API:

Representational-State-Transfer API is a set of architectural set of rules that follow the REST constraints and use HTTP methods to communicate. Those constraints are:

1. Uniform Interface: All must get served the same content irrespective of the device: whether a mobile , pc , or IoT
2. Client/Server: must follow this architecture.Clients are the consumers requesting information, and servers are the providers of that information
3. Stateless: The REST api must not form stateful communications. REST api should serve every request to the client as the first , which means that the client have to send the details every time they send the request. This saves the time and resources for the server to save the details
4. Cacheable: The REST api must indicate that the response is cacheable or not which means storing the commonly requested data by the client. Improves performance , scalability and reduces cost
5. Layered System: The client should be able to request data from the unknown endpoint without knowing the server architecture
6. Code on demand {OPTIONAL} : allows the code to be sent for execution
### But how does it requests:

This REST API request is just an HTTP GET request to the specified URL. In this case, the request queries the store’s inventory for pillows. The provider responds with JSON indicating the item’s ID, name, and quantity of items in stock. If there was an error in the request, the provider would respond with an HTTP error code in the 400 range indicating what went wrong
# Install
just clone the git repo and then run `pip install -r requirements.txt`
and then run with linux: `python3 app.py`
windows: `py -3 app.py`
![[walkthroughs/vampi/IMG/Pasted image 20230923203352.png]]

we are given some various paths that we can use:
![[walkthroughs/vampi/IMG/Pasted image 20230923203547.png]]
# Home page:
![[walkthroughs/vampi/IMG/Pasted image 20230923204244.png]]
