# PLAYPORTAL-GAMELIFT


The source code in this repository gamelift example has been created based on the following example from AWS: https://aws.amazon.com/blogs/gametech/creating-servers-for-multiplayer-mobile-games-with-amazon-gamelift/
Please refer to this guide, when navigating the AWS console to locate the game server script, fleet, server, lambda and IAM profiles for Gamelift. 

#### Current AWS Component Setup (see this config within Code on Canvas aws account)

````
Type: Lambda
Name: GameLiftConnectClientToServer
Usage: Connects the Client Server to the Game Server
Roles: GameLiftConnectClientToServer-role-e8hojpkx 
Role Permissions: 
AWSLambdaBasicExecutionRole-ef5be6b8-a649-490f-8cda-2c362d96f32f Managed policy
GameLiftClientServicePolicy (Custom, minimal access to read / write, see policy description for more information)
````

````
Type: Game Lift (Script)
Name: MegaFrogRaceServer
Usage: Game manager
````

````
Type: GameLift Fleet
ID: fleet-50e4f367-a589-41a8-95b4-5438c3f67e09
Name: MegaFrogRaceFleet
OS: Amazon Linux 2
Fleet Type: On-Demand
EC2 Type: c5.large
Usage: Serves the GameLift Script Instance
````

````
Type: Cognito
Name: MegaFrogRaceAnonPool
No Auth Role:Cognito_MegaFrogRaceAnonPoolUnauth_Role 
Auth Role: Cognito_MegaFrogRaceAnonPoolAuth_Role (not used)
No Auth Role Permissions: lambda:InvokeFunction & mobileanalytics:PutEvents
````

### Costs to run the game server

Approx $50 per month using c5.large. Savings can be made by changing the Fleet server settings utilise spot instances.

### Gamelift Node Js V3 SDK

https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/js-sdk-dg.pdf

### Unoffical node js C# port of SDK

https://github.com/dplusic/GameLift-Nodejs-ServerSDK

### Client server example ts

https://github.com/dplusic/ideal-computing-breaking-machine/blob/master/Client/src/gamelift.ts

### Local setup documentation for linux and windows

https://docs.aws.amazon.com/gamelift/latest/developerguide/integration-testing-local.html

### Components of Gamelift:

#### GameLift Service

Hosts the game script that will drive the game through all connected clients

The easiest way to start a new project is to get GameLift to host a script for you and allocate the compute power for your servers. The compute power is called a fleet, which is a collection of Amazon EC2 virtual machines that will run your game servers.

You can upload a script from an Amazon S3 bucket, or you can directly upload a script/zip file. 

The AWS dashboard provides diagnostics of game sessions and players using the service (useful for debugging and monitoring your live servers).

#### Fleet

A fleet carries an instance of the game server, within an EC2. 

You may choose to use more fleet instances to do the following:

- Serve the game in different time zones
- Serve the game in different versions e.g. demo mode, premium. 
- Allow more users to join, where there is a limit to concurrent users per instance. 

#### Lambda

The lambda in the MegaFrog example is a client service / mediator between the game client and gamelift.

This is a best practice, however you can also communicate directly with the Gamelift SDK.

#### Client Server

- Connect to server
- Dispatch and recieve the event operaction codes

#### Best Practices

See the gamelift document megafrog for the following:

- Live Debugging
- Development flow, build a local version before connecting to the server, so you can see the full game simulation to speed up development. Also create a middle version where you connect the logic to local server e.g. express to manage the events. This should be further researched as to the best way to develop such a tool. 

#### Current progress creating the Node Js connection to Gamelift

##### Run the code:

- cd bitbucket/playportal-gamelift/demo/AWS-Gamelift-resources-examples/
- run ````node index.js```` 
- view the console output within the terminal

##### Notes based on current progress:

The example code below which can be seen inside the client mega frog example, index.js invokes the Lambda function to retrieve game server details. These details and the correct configuration from the client will allow
a player to join a game session, just as they would with the working C# example of the code.

````

  Invoke Lambda function to acquire game session details
  
  lambdaClient.send(request).then(response => {
      if (response.$metadata.httpStatusCode === 200) {
        const response_data = JSON.parse(new TextDecoder("utf-8").decode(response.Payload))
        console.log(response_data);
      }
  });

  payload example from lambda function response

  {      
      PlayerSessionId: 'psess-7f72094a-58af-45d8-b482-5a84b60abffa',
      PlayerId: '424974f7-c951-451f-a462-2542b3d2f3fc',
      GameSessionId: 'arn:aws:gamelift:ap-southeast-2::gamesession/fleet-6a88f5a1-ae32-4de0-b3c7-122a73977efb/gsess-d1f56f64-9e4c-4eee-83e3-d3702c9b3400',
      FleetId: 'fleet-6a88f5a1-ae32-4de0-b3c7-122a73977efb',
      FleetArn: 'arn:aws:gamelift:ap-southeast-2:888062484185:fleet/fleet-6a88f5a1-ae32-4de0-b3c7-122a73977efb',
      CreationTime: '2021-02-03T09:47:15.903Z',
      Status: 'RESERVED',
      IpAddress: '13.236.119.122',
      DnsName: 'ec2-13-236-119-122.ap-southeast-2.compute.amazonaws.com',
      Port: 1900
    }
    
````

In the response above, you will see that the status is 'RESERVED'. This meaning that the Game server instance has reserved a place for the client and the client must make a valid
connection request to activate their status.

Within the C# SDK source code you will see the following details are required to make the connection:

- endpoint (IpAddress from Gamelift Lambda response)
- remoteTcpPort (Port from Gamelift Lambda response)
- listenPort (from my understanding this is the clients UDP Port)
- Token (from the C# SDK this appears to be the PlayerSessionId which is returned from the Lambda response)

````

C# SDK Connect Method:
<param name="endpoint">The endpoint to connect to, for example the IpAddress returned for a game session</param>
<param name="remoteTcpPort">The Realtime server's TCP port, for example the port of the game session</param>
<param name="listenPort">The local client-side UDP listen port</param>
<param name="token">{ PlayerSessionId = ""; Payload = null; }</param>

Also the same parameters are sent via the Megafrog c# example source code shown below:

_connect(string.IsNullOrEmpty(ipAddr) ? DEFAULT_ENDPOINT : ipAddr, port, UDPListenPort, token);

````

(Note: when trying to connect more than 2 player in this example, the reponse will indicate that the maximum number of players has been reached inside the fleet)

### For reference - Node Js Google Protobuf Module 

(for usage please refer to the C# SDK to identify which messages are to be serialised)

https://www.npmjs.com/package/google-protobuf

Segment of text and code taken from the Node.js NPM module google-protobuf (to serialise and deserialise data). 

````

API
The API is not well-documented yet. Here is a quick example to give you an idea of how the library generally works:

var message = new MyMessage();

message.setName("John Doe");
message.setAge(25);
message.setPhoneNumbers(["800-555-1212", "800-555-0000"]);

// Serializes to a UInt8Array.
var bytes = message.serializeBinary();

var message2 = MyMessage.deserializeBinary(bytes);


````

#### Resource links:

https://aws.amazon.com/blogs/gametech/creating-servers-for-multiplayer-mobile-games-with-amazon-gamelift/

https://docs.aws.amazon.com/gamelift/

https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/GameLift.html

https://aws.amazon.com/tools/#sdk

https://docs.aws.amazon.com/gamelift/latest/developerguide/gamelift-dg.pdf

https://docs.amazonaws.cn/en_us/gamelift/latest/developerguide/realtime-script.html

https://docs.aws.amazon.com/gamelift/latest/developerguide/gamelift-dg.pdf#reference-intro

Cognito for Node.js

### Code On Canvas Project Requirements (data/messaging)

For this version of the project users should be able to view the game whilst waiting to play. This means we have 2 maximums; 

1. the number of people able to interact 
2. number of people that can connect/view.

Config, Sent on player connection, contains array of physics variables etc

````
{ 
 objects: [
   {
    name: String,
    index: Int,
    position: Int[THREE.Vector3],
    velocity: Int[THREE.Vector3],
   }
 ],
 players: [
   {
     id: Int
   }
 ],
 queue: Int[]
}
````

Client to Server

````
{
  source: ?,
  event: String,
  data: {
  id: String,
  data: {
    node: Int,
    impulse: Int[THREE.Vector3]
    }
  }
}
````

#### Queue API - for further reading related to Fleet level, rather than within a game session itself. 

https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/GameLift.html

describeGameSessionQueues(params = {}, callback) ⇒ AWS.Request
Retrieves the properties for one or more game session queues.

describeGameSessions(params = {}, callback) ⇒ AWS.Request
Retrieves a set of one or more game sessions.

describeInstances(params = {}, callback) ⇒ AWS.Request
Retrieves information about a fleet's instances, including instance IDs.

describePlayerSessions(params = {}, callback) ⇒ AWS.Request
Retrieves properties for one or more player sessions.

describeGameSessionDetails(params = {}, callback) ⇒ AWS.Request
Retrieves properties, including the protection policy in force, for one or more game sessions.

describeGameSessionPlacement(params = {}, callback) ⇒ AWS.Request
Retrieves properties and current status of a game session placement request.

describeGameSessionQueues(params = {}, callback) ⇒ AWS.Request
Retrieves the properties for one or more game session queues.

describeGameSessions(params = {}, callback) ⇒ AWS.Request
Retrieves a set of one or more game sessions.




