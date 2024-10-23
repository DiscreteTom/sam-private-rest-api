# sam-private-rest-api

This demo project shows how to use AWS SAM to create a private REST API with AWS Lambda and Amazon API Gateway.

```diff
  AWSTemplateFormatVersion: "2010-09-09"
  Transform: AWS::Serverless-2016-10-31
  Description: >
    sam-private-rest-api

    Sample SAM Template for sam-private-rest-api

+ Parameters:
+   AllowedVpcEndpointId:
+     Type: String

  Resources:
    ServerlessApi:
      Type: AWS::Serverless::Api
      Properties:
        StageName: Prod
+       EndpointConfiguration:
+         Type: PRIVATE
+         VPCEndpointIds:
+           - !Ref AllowedVpcEndpointId
+       Auth:
+         ResourcePolicy:
+           IntrinsicVpceWhitelist:
+             - !Ref AllowedVpcEndpointId

    HelloWorldFunction:
      Type: AWS::Serverless::Function
      Properties:
        CodeUri: hello-world/
        Handler: app.lambdaHandler
        Runtime: nodejs20.x
        Architectures:
          - x86_64
        Events:
          HelloWorld:
            Type: Api
            Properties:
+             RestApiId: !Ref ServerlessApi
              Path: /hello
              Method: get

  Outputs:
    HelloWorldApi:
      Description: "API Gateway endpoint URL for Prod stage for Hello World function"
+     Value: !Sub "https://${ServerlessApi}-{AllowedVpcEndpointId}.execute-api.${AWS::Region}.amazonaws.com/Prod/hello/"
```

For more information, see:

- [Private REST APIs in API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-apis.html).
- [AWS::Serverless::Api](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-api.html).
