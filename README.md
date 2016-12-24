# mnawslambdainact
##1. Running functions in the cloud
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


