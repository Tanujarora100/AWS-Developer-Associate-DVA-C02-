# Lambda
- Pay per request (number of invocations) and compute time
- Free tier: 1,000,000 requests and 400,000 GBs of compute time
- Docker containers supported

## Performance & Limits

- RAM: 128MB (default) - 10 GB (1 MB increments)
- Disk capacity (`/tmp`): 512 MB (free) - 10 GB maximum.
- Cannot configure vCPU count directly, increase RAM to increase vCPU count
    - RAM = 1,792 MB ⇒ vCPU = 1
    - For vCPU > 1, need to use multi-threading to take advantage of multiple cores
- Timeout: default 3s, max 15 mins (900s)
- Environment variables: max 4 KB of environment variables can be stored.
- Deployment
    - Compressed: max 50 MB
    - Uncompressed: max 250 MB (for more use `/tmp`)

## Synchronous Invocation
- Error handling (retries, exponential backoff, etc) must be done by the caller
- Invoking a lambda function using CLI or SDK results in synchronous invocations
- To invoke explicitly, set `invocation-type` to `RequestResponse`
- Some services that invoke the lambda function synchronously
    - Invoked by user
        - ALB
        - API Gateway
        - CloudFront (Lambda@Edge)
        - S3 Batch
    - Invoked by service
        - Cognito
        - Step Functions
        - **SQS** (through event source mapping)
## Asynchronous Invocation

- The caller just invokes the lambda function and does not wait for the result. Can send many events and let lambda process them.
- The event is put in an event queue from which the lambda function reads
- Invoking a lambda function asynchronously returns **status code 202 (lambda function invoked)**. We don’t know if the processing was successful or not.
- For asynchronous invocation, set `invocation-type` to `Event`

- Error handling is done by the lambda function
    - Retries: 0 - 2
    - Exponential Backoff: 1 min (first retry), 2 min (second retry)
    - Can setup DLQ (SQS queue or an SNS topic) **on the lambda function** to send failed events after retries. Need to provide IAM permissions to the lambda to write to DLQ.
    - Retries lead to duplicate logs in CW
    - Make sure the event processing is **idempotent** (not affected by retries)
- Some services that invoke the lambda function asynchronously
    - S3 (S3 notifications)
    - **SNS**
    - EventBridge (events in event buses)
    - CodeCommit (Code Commit Trigger: new branch, new tag, new push)
    - CodePipeline (invoke a Lambda function during the pipeline)