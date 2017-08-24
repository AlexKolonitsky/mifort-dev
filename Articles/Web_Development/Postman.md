### What is it?

Postman is a HTTP client for storing and testing server API. 
First, it is sending requests. Let’s say you have a Rest endpoint without authentication and want to test it. Choose the method, enter the URL, edit params or body of request and send it.

Magic! Check the response:

All sent requests are stored in the History. Open the History tab in the left sidebar.

History is a good thing, indeed, but at the same time it is inconvenient to look for the specific response in the long history if you used the application for a long period of time. What if I want to save one of requests or a group of them?

### Collections

Collection is like a folder. You can just put any request to it, or create the folders inside to create your own hierarchy. 

Press “Save” button.

Enter the name and description of request and choose collection or enter name of new one.

Now you can easily find the requests or the whole collections and work with them.

It would be great to share your collection with anyone. Or get collection from someone, right? That is why collections can be exported to a file and imported from it. Awesome!

### Environments

There are often situations when some data enters into separate variables. For example, it can be a long repeating url for all requests, username, access tokens, etc. You can create an environment with variables that contain this data, and then use them in requests.
Environments are creating in “Manage Environments”.

Add the new environment: enter a name of it and the parameters. Press “Add” button to save.

Now you can choose your environment and use it with the following syntax: `{{parameter}}`.

You can export or import environments same as collections.

### Tests

Postman can test your requests. Testing occurs after receiving a response. You can check the response to the presence of the headers, expected data, received status, etc. An example of the test:

Result of the test:

The topic of tests is worth a separate article. I'll speak about writing tests, using "Runner" to run a group of tests and view statistics, as well as about pre-request scripts.
	
### Why Postman?
There are analogues of Postman: Paw, Insomnia, Swagger, API Tester and ect. However, Postman does not come short of its popularity because of its simplicity and speed. The creation and the sending of the request takes no more than a minute. The collection storage is very convenient. Tests can be written and run without any additional setup. It is awesome application for anyone who want to test REST API.
	
