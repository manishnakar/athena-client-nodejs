# athena-client-nodejs
a nodejs simple aws athena client

Install with:

    npm install athena-client

## Usage Example

### Create Client
```js
var clientConfig = {
    bucketUri: 's3://xxxx'
}

var awsConfig = {
    region: 'xxxx', 
}
 
var athena = require("athena-client")
var client = athena.createClient(clientConfig, awsConfig)
```

### Receive result by Callback
```js
client.execute('SELECT 1', function(err, data) {
    if (err) {
        return console.error(err)
    }
    console.log(data)
})
```

### Receive result by Promise
```js 
client.execute('SELECT 1').toPromise()
.then(function(data) {
    console.log(data)
})
.catch(function(err) {
    console.error(err)
})
```

### Receive result by Stream
```js
var stream = client.execute('SELECT 1').toStream()
stream.on('data', function(record) {
  console.log(record)
})
stream.on('query_end', function(queryExecution) {
  console.log(queryExecution)
})
stream.on('end', function() {
  console.log('end')
})
stream.on('error', function(e) {
  console.error(e)
})
```

# API
### athena = require("athena-client")
This module exposes the `createClient` and `setConcurrentExecMax` method, which execute query to AWS Athena.

### client = athena.createClient([_clientConfig_], [_awsConfig_])
Returns a client instance attached to the account specified by the given `clientConfig` and `awsConfig`.

### athena.setConcurrentExecMax([_concurrentExecMax_])
Set the number of cuncurrent execution of query max. It should be set `smaller than AWS Service limit` (default is 5).



### client.execute([_query_], [_callback_])
It will return the following result.
If you want to know more about params of `queryExecution`, please refer to the `aws-sdk` [document](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/Athena.html#getQueryExecution-property)  

```json
{
    "records": [
        {"_col0:": "1"}
    ],
    "queryExecution": {
        "Query": "SELECT 1", 
        "QueryExecutionId": "55571bb9-8e4e-4274-90b7-8cffe4539c3c", 
        "ResultConfiguration": {
            "OutputLocation": "s3://bucket/55571bb9-8e4e-4274-90b7-8cffe4539c3c"
        }, 
        "Statistics": {
            "DataScannedInBytes": 0, 
            "EngineExecutionTimeInMillis": 137
        }, 
        "Status": {
            "CompletionDateTime": "2017-12-31T16:03:53.493Z", 
            "State": "SUCCEEDED", 
            "SubmissionDateTime": "2017-12-31T16:03:53.209Z"
        }
    }
}
```

### client.execute([_query_]).toPromise()
Returns a `Promise` that resolves the result of your query.

### client.execute([_query_]).toStream()
Returns a `Stream` to buffer the results of your query. This method is recommended for **large** result sets.

```js
// Get record one by one
stream.on('data', function(record) {
  console.log(record) // {"col1": "val1", "col2": "val2"}
})

// When query succeed, this event will emit.
stream.on('query_end', function(queryExecution) {
  console.log(queryExecution) // {"QueryExecutionId": "", ...}
})

stream.on('end', function() {
  console.log('end')
})
stream.on('error', function(e) {
  console.error(e)
})
```



