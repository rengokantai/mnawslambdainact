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


    
