# sls-offline-dynamodb-streams

This sls-offline-dynamodb-streams plugin emulates AWS λ and DynamoDBStreams on your local machine. To do so, it listens DynamoDBStreams stream and invokes your handlers.

_Features_:

- [Serverless Webpack](https://github.com/serverless-heaven/serverless-webpack/) support.
- DynamoDBStreams configurations: batchsize and startingPosition.

## Installation

First, add `sls-offline-dynamodb-streams` to your project:

```sh
npm install sls-offline-dynamodb-streams
```

Then inside your project's `serverless.yml` file, add following entry to the plugins section before `serverless-offline` (and after `serverless-webpack` if presents): `sls-offline-dynamodb-streams`.

```yml
plugins:
  - serverless-webpack
  - sls-offline-dynamodb-streams
  - serverless-offline
```

[See example](../sls-offline-dynamodb-streams-integration/README.md)

## Configure

### Functions

Ths configuration of function of the plugin follows the [serverless documentation](https://serverless.com/framework/docs/providers/aws/events/streams/).

```yml
functions:
  myKinesisHandler:
    handler: handler.compute
    events:
      - stream:
          enabled: true
          type: dynamodb
          arn: arn:aws:dynamodb:eu-west-1:XXXXXX:table/myStream/stream/2018-07-02T19:48:31.121
          batchSize: 10
          startingPosition: TRIM_HORIZON
```

### DynamoDB

The configuration of [`aws.DynamoDBStreams`'s client](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDBStreams.html#constructor-property) of the plugin is done by defining a `custom: sls-offline-dynamodb-streams` object in your `serverless.yml` with your specific configuration.

You could use [mhart's Dynalite](https://github.com/mhart/dynalite) with the following configuration:

```yml
custom:
  sls-offline-dynamodb-streams:
    apiVersion: "2013-12-02"
    endpoint: http://0.0.0.0:8000
    region: eu-west-1
    accessKeyId: root
    secretAccessKey: root
    skipCacheInvalidation: false
    readInterval: 500
```

> `arn` could be deduce from `tableName` if your add the key `tableName` in your function's configuration. Useful if your use dynalite and regularly recreate a new DynamoDBStreams.

```yml
functions:
  myKinesisHandler:
    handler: handler.compute
    events:
      - stream:
          enabled: true
          type: dynamodb
          tableName: myTable
```
