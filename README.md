<!--
title: 'AWS Simple HTTP Endpoint example in NodeJS'
description: 'This template demonstrates how to make a simple HTTP API with Node.js running on AWS Lambda and API Gateway using the Serverless Framework.'
layout: Doc
framework: v3
platform: AWS
language: nodeJS
authorLink: 'https://github.com/serverless'
authorName: 'Serverless, inc.'
authorAvatar: 'https://avatars1.githubusercontent.com/u/13742415?s=200&v=4'
-->

## Node.js API with Serverless Framework in AWS Environment

This is an AWS cloud infrastructure builded with API Gateway, DynamoDB, AWS Lambda, and AWS CloudFormation using the Serverless framework for infrastructure development based on Infrastructure as Code.

## Steps

Prerequisites: 
 - Have an AWS account and install Node.js on the machine.
 - Install the AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html

### Initial Setup

#### AWS Credentials

- Create a user: AWS Management Console -> IAM Dashboard -> Create New User -> <username> -> Permissions "Administrator Access" -> Programmatic Access -> Download Keys
- In the terminal: $ aws configure -> paste the previously generated credentials
  
#### Set up the Serverless framework
```$ npm i -g serverless```

### Project Development
  
```
$ serverless
Login/Register: No
Update: No
Type: Node.js REST API (V1) or Node.js HTTP API (V2)
Name: aws-serverless
```
```
$ cd aws-serverless
$ code .
``` 
- In the ```serverless.yml``` file, add the region ```region: us-east-1``` within the ```provider:``` scope.
- Save and deploy ```$ serverless deploy -v```
  
#### Code Structure

- Create the "src" directory and move the "handler.js" file into it.
- Rename the "handler.js" to "hello.js"
- Update the code:
```
const hello = async (event) => {
/////
module.exports = {
    handler:hello
}
```
- Update the "serverless.yml" file:
```
handler: src/hello.handler
```
```$ serverless deploy -v ```

#### DynamoDB
Update the serverless.yml file:
```
resources:
  Resources:
    ItemTable:
      Type: AWS::DynamoDB::Table
      Properties:
          TableName: ItemTable
          BillingMode: PAY_PER_REQUEST
          AttributeDefinitions:
            - AttributeName: id
              AttributeType: S
          KeySchema:
            - AttributeName: id
              KeyType: HASH
```
#### Develop Lambda Functions

	- Folder  /src in the repository.
 	- Get the ARN of the table in DynamoDB AWS Console -> DynamoDB -> Overview -> Amazon Resource Name (ARN).
	- Update the serverless.yml file with the following code, below ```region:```
  ```
	iam:
      role:
          statements:
            - Effect: Allow
              Action:
                - dynamodb:PutItem
                - dynamodb:UpdateItem
                - dynamodb:GetItem
                - dynamodb:Scan
              Resource:
                - arn:aws:dynamodb:us-east-1:167880115321:table/ItemTable
  ```
  
   - Install dependencies:

   ```npm init```
   ```npm i uuid aws-sdk```
   
  - Update the list of functions in the serverless.yml file:
  ```
  functions:
  hello:
    handler: src/hello.handler
    events:
      - http:
          path: /
          method: get
  insertItem:
    handler: src/insertItem.handler
    events:
      - http:
          path: /item
          method: post
  fetchItems:
    handler: src/fetchItems.handler
    events:
      - http:
          path: /items
          method: get
  fetchItem:
    handler: src/fetchItem.handler
    events:
      - http:
          path: /items/{id}
          method: get
  updateItem:
    handler: src/updateItem.handler
    events:
      - http:
          path: /items/{id}
          method: put
  ```

## Serverless Framework Node HTTP API on AWS

This template demonstrates how to make a simple HTTP API with Node.js running on AWS Lambda and API Gateway using the Serverless Framework.

This template does not include any kind of persistence (database). For more advanced examples, check out the [serverless/examples repository](https://github.com/serverless/examples/) which includes Typescript, Mongo, DynamoDB and other examples.

### Usage

#### Deployment

```
$ serverless deploy
```

After deploying, you should see output similar to:

```bash
Deploying aws-node-http-api-project to stage dev (us-east-1)

✔ Service deployed to stack aws-node-http-api-project-dev (152s)

endpoint: GET - https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/
functions:
  hello: aws-node-http-api-project-dev-hello (1.9 kB)
```

_Note_: In current form, after deployment, your API is public and can be invoked by anyone. For production deployments, you might want to configure an authorizer. For details on how to do that, refer to [http event docs](https://www.serverless.com/framework/docs/providers/aws/events/apigateway/).

#### Invocation

After successful deployment, you can call the created application via HTTP:

```bash
curl https://xxxxxxx.execute-api.us-east-1.amazonaws.com/
```

Which should result in response similar to the following (removed `input` content for brevity):

```json
{
  "message": "Go Serverless v2.0! Your function executed successfully!",
  "input": {
    ...
  }
}
```

#### Local development

You can invoke your function locally by using the following command:

```bash
serverless invoke local --function hello
```

Which should result in response similar to the following:

```
{
  "statusCode": 200,
  "body": "{\n  \"message\": \"Go Serverless v3.0! Your function executed successfully!\",\n  \"input\": \"\"\n}"
}
```


Alternatively, it is also possible to emulate API Gateway and Lambda locally by using `serverless-offline` plugin. In order to do that, execute the following command:

```bash
serverless plugin install -n serverless-offline
```

It will add the `serverless-offline` plugin to `devDependencies` in `package.json` file as well as will add it to `plugins` in `serverless.yml`.

After installation, you can start local emulation with:

```
serverless offline
```

To learn more about the capabilities of `serverless-offline`, please refer to its [GitHub repository](https://github.com/dherault/serverless-offline).
