# gamelift

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
