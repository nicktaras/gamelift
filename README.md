# gamelift

# list ports
netstat -a

## unoffical

https://github.com/dplusic/GameLift-Nodejs-ServerSDK/blob/master/src/Server/Network.ts

## Client server example ts
https://github.com/dplusic/ideal-computing-breaking-machine/blob/master/Client/src/gamelift.ts

## Must Use / Learn

https://docs.aws.amazon.com/gamelift/latest/developerguide/integration-testing-local.html

## Components:

### GameLift

Hosts the game script that will drive the game through all connected clients

The easiest way to start a new project is to get GameLift to host a script for you and allocate the compute power for your servers. The compute power is called a fleet, which is a collection of Amazon EC2 virtual machines that will run your game servers.

It lets you upload a script from an Amazon S3 bucket, or you can directly upload a script/zip file. 

### Fleet

A fleet carries an instance of the game server, within an EC2. 

You may choose to use more fleet instances to do the following:

- Serve the game in different time zones
- Server the game in different versions e.g. demo mode, premium. 
- Allow more users to join, where there is a limit to concurrent users per instance. 

### Lambda

The lambda in the MegaFrog example is a client service / mediator between the game client and gamelift.

This is a best practice, however you can communicate directly with the Gamelift SDK.

### Client Server

- Connect to server
- Dispatch and recieve the event operaction codes

## Best Practices

See the gamelift document megafrog for the following:

- Live Debugging
- Development flow, build a local version before connecting to the server, so you can see the full game simulation to speed up development. Also create a middle version where you connect the logic to local server e.g. express? To manage the events. This should be further researched as to the best way to develop such a tool. 

https://aws.amazon.com/blogs/gametech/creating-servers-for-multiplayer-mobile-games-with-amazon-gamelift/

https://docs.aws.amazon.com/gamelift/

https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/GameLift.html

https://aws.amazon.com/tools/#sdk

https://docs.aws.amazon.com/gamelift/latest/developerguide/gamelift-dg.pdf

Code:

https://docs.amazonaws.cn/en_us/gamelift/latest/developerguide/realtime-script.html

Guide:

https://docs.aws.amazon.com/gamelift/latest/developerguide/gamelift-dg.pdf#reference-intro

Cognito, js

// Initialize the Amazon Cognito credentials provider
AWS.config.region = 'ap-southeast-2'; // Region
AWS.config.credentials = new AWS.CognitoIdentityCredentials({
    IdentityPoolId: '...',
});

Cognito, .net

// Initialize the Amazon Cognito credentials provider
CognitoAWSCredentials credentials = new CognitoAWSCredentials(
    "ap-southeast-2:9a229c33-7832-4924-8fa4-f12f60c2fc50", // Identity pool ID
    RegionEndpoint.APSoutheast2 // Region
);

:(
https://answers.unity.com/questions/776835/aws-sdk-the-namespace-amazon-could-not-be-found.html

