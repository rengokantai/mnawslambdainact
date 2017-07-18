# mnawslambdainact
## 1. Running functions in the cloud
AWS Lambda implements the execution of those functions with an efficient use of the underlying compute resources that allows for an interesting and innocative cose model.
With AWS Lambda you pay for 
- The number of invocations
- The hundreds of milliseconds of execution time of all invocations, depending on the memory given to the functions

Each month there's no charge for
- The first one million invocations
- The first 400000 seconds of execution the with 1GB of memory

simple function
```js
exports.handler = (event,context,cb)=>{
  var result = event.value1+event.value2;
  cb(null,result);
}
```
```py
def lambda_handler(event,context):
  result = event['value1']+event['value2']
  return result
```

data
```
{"value1":1,"value2":2}
```



Using Lambda to implement a simple logging function
```js
exports.handler = (event,context){
  console.log(event.message);
  context.done();
};
```

```py
def lambda_handler(event,context):
  print(event['message'])
  return
```
data
```
{ "message":"ok"}
```



##2. Your first Lambda function
```js
exports.handler = (event,context,cb)=>{
  JSON.stringify(event,null,2);
  var name='';
  if('name' in event){
    name=event['name']
  }
  else{
    name='k';
  }
  var greet = 'hello'+name;
  cb(null,greet);
}
```

```py
import json
def lambda_handler(event,context):
  json.dumps(event,indent=2);
  if 'name' in event:
    name=event['name']
  else:
    name='k'
  greet = 'hello'+name;
  return greet
```

##3. Your function as a web API
function:
```
exports.handler = (event,context,cb)=>{
  JSON.stringify(event,null,2);
  var name='';
  if('name' in event){
    name=event['name']
  }
  else{
    name='k';
  }
  var greet = 'hello'+name;
  cb(null,greet);
}
```
create new API
create new child resource  
add new method (GET)  
create integration (GET) ->lambda function  
method request: add parameter (name)  
integration request: add
```
{ "name": "$input.params('name')" }
```
###5. Transforming the response
select Integration Response  
expand Body Mapping Templates  
```
{ "greet": "$input.path('$')" }
```
Click the Test button to actually run a new test and overwrite the result.  
Now the response body has a fully compliant JSON syntax.  

To change the default behavior for our REST API, using an #if ... #end block. 
```
#set($name = $input.params('name'))
{
#if($name != "")
  "name": "$name"
#end
}
```

### 3.7. Using the API Gateway context
$context
```
$context.identity.sourceIp
```

```js
exports.handler = (event, context, callback) => {
  callback(null, event.myip);
};
```
```py
def lambda_handler(event, context):
    return event['myip']
```

## 13. Improving development and testing
### 13.1. Developing locally
A few projects have been developed by the AWS community which you could use to emulate Amazon S3. For example, FakeS3 is a lightweight server that responds to the same calls that Amazon S3 responds to. Another option is Minio, an Amazon S3–compatible object-storage server. For more information on how to use FakeS3 and Minio, see  
[fake-s3](https://github.com/jubos/fake-s3)  
[minio](https://github.com/minio/minio)  


###### tip
The only downside I can see in using a live AWS environment for development is that you need a (decent) internet connection, but is that a limitation?  

#### 13.1.1. Developing locally in Node.js
function greetingOnDemand(Node.js)
```
exports.handler = (event,context,cb)=>{
  JSON.stringify(event,null,2);
  var name='';
  if('name' in event){
    name=event['name']
  }
  else{
    name='k';
  }
  var greet = 'hello'+name;
  cb(null,greet);
}
```
runLocal
```
var lambdaFunction = require('./greetingOnDemand');
var functionHandler = 'handler';
var event={};
var context ={};

function callback(err,data){
  console.log(err);
  console.log(data);
}
lambdaFunction[functionHandler](event,context,callback);
```

#### 13.1.2. Developing locally in Python
greetingOnDemand
```py
import json
def lambda_handler(event,context):
  json.dumps(event,indent=2);
  if 'name' in event:
    name=event['name']
  else:
    name='k'
  greet = 'hello'+name;
  return greet
```

runlocal.py  
```py
import importlib
mod = importlib.import_module('greetingOnDemand')
functionHandler = 'lambda_handler'
lambdaFunction = getattr(mod,functionHandler)

event={'name':'k'}
context={}
try:
    data=lambdaFunction(event,context)
    print data
except Exception as error:
    print error
    
```

#### 13.1.3. Community tools
[lambda-local] (https://github.com/ashiina/lambda-local)
[aws-lambda-python-local](https://github.com/sportarchive/aws-lambda-python-local)
### 13.2. Logging and debugging
Each function has a CloudWatch log group with a name starting with ```/aws/lambda/```, 
followed by the function name. For example:  
```
/aws/lambda/greetingsOnDemand
```
You can use the AWS CLI to check the logs of your Lambda functions, using the following command example:
```
aws logs get-log-events --log-group-name /aws/lambda/<FUNCTION_NAME> --log-stream-name 'YYYY/MM/DD/[$LATEST]...'
```

An example of an Unqualified ARN for the helloWorld function is the following code:
```
arn:aws:lambda:<REGION>:<ACCOUNT_ID>:function:helloWorld
```
If you want to be more specific, you can point at the latest version using this Qualified ARN:
```
arn:aws:lambda:<REGION>:<ACCOUNT_ID>:function:helloWorld:$LATEST
```
For example, to use version 3 of a function, you can use a Qualified ARN ending with “:3”:
```
arn:aws:lambda:<REGION>:<ACCOUNT_ID>:function:helloWorld:3
```

### 13.4. Using aliases to manage different environments
For example, the most recent version is probably the one you’re currently working on in the development environment. Before going into production, a function can go through different stages of testing, such as an integration test or a user-acceptance test.  

###### Note
You cannot use an Unqualified ARN when creating or updating an alias. Only __Qualified ARNs__ are accepted by AWS Lambda in this case.

######
Starting from the example in figure 13.2, if UI tests on version 3 complete correctly, you may want to move that version into production and start new UI tests for version 4. You can update the alias UITest to point to version 4 and the alias Production to version 3.




#### 13.5.1. Chalice Python microframework
```
chalice new-project greetingsOnDemand
cd greetingsOnDemand
chalice deploy
```

#### 13.5.2. Apex serverless architecture
```
curl https://raw.githubusercontent.com/apex/apex/master/install.sh | sh
```
(windows download binary)
```
mkdir testapex && cd testapex
apex init
apex deploy
```

test the function
```
apex invoke hello
```

#### 13.5.3. Serverless Framework
```
npm install -g serverless
```

create a server (you dont need to create the folder first) (tested, -path/--path will fail)
```
serverless create --template aws-nodejs –p my-service
serverless deploy
```
invoke function
```
serverless invoke --function hello
```
remove stack
```
serverless remove
```
### 13.6. Simple serverless testing
lambdaTest
```
'use strict'
let AWS = require('aws-sdk');
let doc = require('dynamodb-doc');
let lambda = new AWS.Lambda({apiversion:'2015-03-31'});
let dynamo = new doc.DynamoDB();

const asyncAll = (opts)=>{
  let i=-1;
  const next = ()=>{
    i++;
    if(i===opts.times){
      opts.done();
      return;
    }
    opts.fn(next,i);
  };
  next();
}

const unit = (event,cb)=>{
  const lambdaParams={
    FunctionName:event.function,
    Payload:JSON.stringify(event.event);
  };
  lambda.invoke(lambdaParams,(err,data)=>{
    if(err){return cb(err);}
    const dynamoParams={
      TableName:event.resultsTable,
      Item:{
        testId:event.testId,
        ieration:event.iteration||0,
        result:data.Payload,
        passed:!JSON.parse(data.Payload).hasOwnProperty('errorMessage')
      }
  };
  dynamo.putItem(dynamoParams,cb);
});
};
const load = (event,cb)=>{
  const payload = event.event;
  asyncAll({
    times:event.iterations,
    fn:(next,i)=>{
      payload.iteration=i;
      const lambdaParams={
        FunctionName:event.function,
        invocationType:'Event',
        Payload:JSON.stringify(payload)
      };
      lambda.invoke(lambdaParams,(err,data)=>next());
    },
    done:()=>cb(null,'complete')
  });
};

const ops={
  unit:unit,
  load:load
}


exports.handler=(event,context,cb)=>{
  if(ops.hasOwnProperty(event.operation)){
    ops[event.operation](event,cb);
  } else{
    cb(`${event.operation}`);
  }
}
```
To run the unit test, you should pass the following event, specifying all necessary information, as input to the lambdaTest function to execute the test. For example:
```
{
  "operation": "unit",
  "function": "greetingsOnDemand",
  "event": { "name": "John" },
  "resultsTable": "myResultTable",
  "testId": "myTest123"
}
```
To run a load testing with 50 invocations of the previous functions, you need to change the operation to load and add the number of iterations you need (you don’t need the DynamoDB table anymore). For example:
```
{
  "operation": "load",
  "iterations": 50,
  "function": "greetingsOnDemand",
  "event": { "name": "John" }
}
```


### Exercise





## 14. Automating deployment
### 14.1. Storing code on Amazon S3
```
aws s3 mb s3://<BUCKET>
zip -r ../greetingsOnDemand-v1 .
aws s3 cp greetingsOnDemand-v1.zip s3://<BUCKET>/code/
```
create lambda function
```
aws lambda create-function  \
    --function-name anotherGreetingsOnDemand \
    --code S3Bucket=<BUCKET>,S3Key=code/greetingsOnDemand-v1.zip \
    --runtime nodejs4.3 \
    ––role arn:aws:iam::123412341234:role/lambda_basic_execution \
    --handler index.handler
```
