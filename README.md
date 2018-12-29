### This library generates swagger-like UI with serverless.yml file of serverless framework.

##### Demo
https://conanshin.github.io/aws-api-documentor/

##### How to setup
1. ```npm install```
2. Place your yml/yaml file in assets directory with name serverless.yml

##### How to start server
```
npm run serve
```

##### How to configure
Declare Query/Body Parameters keys and default values as below in your yml/yaml file.
```
functions:
  sampleLambdaFunction1:
    events:
      - http:
          path: sample/api/url (required)
          method: get (required)
          ...
          ...
          queryParameters: (optional)
            queryParamKey1: 'queryParameter1'
            queryParamKey2: 'queryParameter2'
          bodyParameters: (optional)
            bodyParamKey1: 'bodyParameter1'
            bodyParamKey2: 'bodyParameter2'
```

##### Todo
1. Dynamically pass yml/yaml file directory on starting server
