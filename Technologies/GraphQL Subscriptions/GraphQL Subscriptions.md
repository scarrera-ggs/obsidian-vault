
## What is a GraphQL Subscription

Subscriptions are a GraphQL feature that allows a server to send data to its clients when a specific _event_ happens. 

i.e. You create a subscription to be notified every time there is a change in the users table of your app. So, every time a user is created, modified or deleted GraphQL will send a "notification" instantly to you (if you are subscribe) without you needing to make a request for that information.


## How to implement a GraphQL Subscription

### Before implementing a subscription (authentication)

For robust API's frequently developers build an authentication mechanism to access the API. In Generac Grid Services OAuth was build in order to access resources within the API. So, before implementing any subscriptions we must create an OAuth application that target the specific scope of the subscription to get a username and password.

To create an OAuth application follow instructions below:

1. Create OAuth application
	- Go to `{{graphql_root_url}}/oatuh/applications/new`.
	- Name = Adapter name as it appears on the adapters table from the Postgres DB.
	- Redirect uri = WIP, not too much information a bout this field, it can be anything.
	- Scopes = The scope of the subscription, it can be found on the GraphQL schema docs.
	- Click on sabe.

![[OAuth create application.png]]

2. Collect ID and Secret

![[OAuth credentials.png]]

3. Request access token issuing a mutation to the server using the credentials stored from previous step.


### Subscriptions using Websockets

Subscriptions are usually implemented with WebSockets. In that setup, the server maintains a steady connection to its subscribed client. This also breaks the “Request-Response-Cycle” that were used for all previous interactions with the API.

Instead, the client initially opens up a long-lived connection to the server by sending a _subscription query_ that specifies which _event_ it is interested in. Every time this particular event happens, the server uses the connection to push the event data to the subscribed client(s).

This approach has a big ***drawback***. Keeping a connection alive through a socket is a hard task, there are multiple reasons why a server can go down. If the GraphQL server goes down, the connection to the socket will be interrupted. So, there must be a built-in mechanism to retry connecting to the server, persist the connection, and subscribe again. This leads to a **low availability** of the service which will impact high scalability of products.


### Subscriptions using SQS

Subscriptions needs a *transport* mechanism to tell the client that an event occurred on the server. By implementing a subscription through SQS the transport mechanism is not anymore websockets. A SQS resource must be created in AWS and the means to write data to that queue must be provided in the header of the subscription.

From an adapters point of view to create a subscription trough SQS the adapter must:
1. Create an SQS queue on AWS.
2. Create an IAM user with permission to write to SQS queue.
3. Send a subscription request and configure the transport mechanism to point the SQS resource, the information needed is:
	- Transport: SQS
	- SQS url
	- IAM user credentials
	- IAM user token
	These information must be provided in the header of the request.
4. Process the data received from the subscription using a AWS Lambda.


