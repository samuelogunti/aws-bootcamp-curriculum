# Module 09: Quiz

1. Which of the following are characteristics of serverless computing with AWS Lambda? (Select THREE.)

   A) You provision and manage the underlying EC2 instances
   B) Lambda scales automatically from zero to thousands of concurrent executions
   C) You pay only for the compute time your code consumes, measured in milliseconds
   D) Lambda functions can run indefinitely with no maximum execution time
   E) There is no charge when your function is not running

2. A Lambda function's handler is defined as `app.process_order`. Which of the following correctly describes what this means?

   A) The function is in a file named `handler.py` with a method named `app.process_order`
   B) The function is in a file named `app.py` with a method named `process_order`
   C) The function is in a folder named `app` with a file named `process_order.py`
   D) The function is in a class named `app` with a static method named `process_order`

3. True or False: The execution environment lifecycle consists of three phases (Init, Invoke, and Shutdown), and the Init phase runs once per execution environment while the Invoke phase repeats for each subsequent invocation that reuses the same environment.

4. Your team runs a Lambda function that processes messages from an Amazon SQS queue. A colleague argues that the SQS service pushes messages directly to the Lambda function, similar to how Amazon S3 invokes Lambda on object upload. Explain why this is incorrect, and describe how Lambda actually receives messages from SQS.

5. A developer creates a Lambda function but forgets to attach any permissions policy to the execution role beyond `AWSLambdaBasicExecutionRole`. The function attempts to read an item from a DynamoDB table. What happens?

   A) The function succeeds because Lambda automatically grants DynamoDB access to all functions
   B) The function fails with an `AccessDeniedException` because the execution role lacks DynamoDB permissions
   C) The function succeeds on the first invocation but fails on subsequent invocations
   D) The function times out because DynamoDB rejects the connection silently

6. Which of the following statements about Lambda environment variables is correct?

   A) Environment variables are stored in plaintext and cannot be encrypted
   B) Lambda encrypts environment variables at rest using AWS Key Management Service (AWS KMS)
   C) Environment variables can only be set at function creation time and cannot be changed later
   D) Environment variables are shared across all functions in the same AWS account

7. A company deploys a Python Lambda function with a 50 MB deployment package that imports several large libraries during initialization. The function is invoked infrequently (a few times per hour). Users report that some requests take 3 to 4 seconds, while others complete in under 200 milliseconds. The team wants to reduce the latency for the slow requests without changing the function code. Which combination of strategies would be most effective? (Select TWO.)

   A) Increase the function timeout from 30 seconds to 120 seconds
   B) Configure provisioned concurrency to keep execution environments pre-initialized
   C) Reduce the deployment package size by removing unused dependencies and using a Lambda Layer for shared libraries
   D) Change the function's reserved concurrency to zero
   E) Switch the function runtime from Python to Java for faster cold starts

8. A Lambda function packages a shared data-processing library that three other Lambda functions also need. Every time the library is updated, the team must redeploy all four functions. Which Lambda feature solves this problem by allowing the library to be packaged and versioned independently?

   A) Lambda aliases
   B) Lambda Layers
   C) Lambda extensions
   D) Lambda destinations

9. An e-commerce company is building a serverless REST API. When a customer places an order, the API Gateway endpoint receives a POST request and invokes a Lambda function using proxy integration. The Lambda function processes the order and writes it to DynamoDB. During testing, the API returns `{"message": "Internal server error"}` for every request, but the Lambda function works correctly when invoked directly from the CLI. Which of the following is the most likely cause?

   A) The DynamoDB table does not exist in the same Region as the Lambda function
   B) The Lambda function's response does not include the required `statusCode`, `headers`, and `body` fields in the format expected by API Gateway proxy integration
   C) The API Gateway REST API has not been deployed to a stage
   D) The Lambda function's execution role does not have permission to be invoked by API Gateway

10. A startup wants to estimate the monthly cost of a Lambda function that receives 5 million invocations per month, with each invocation running for 200 milliseconds using 256 MB of memory. Which two factors directly determine the Lambda compute cost for this workload?

    A) The number of invocations and the amount of data stored in the function's `/tmp` directory
    B) The number of invocations and the total GB-seconds of compute time (duration multiplied by memory)
    C) The deployment package size and the number of Lambda Layers attached
    D) The number of CloudWatch log entries and the API Gateway request count

---

<details>
<summary>Answer Key</summary>

1. **B, C, E**
   Lambda scales automatically (B), charges only for compute time in milliseconds (C), and has no charge when idle (E). You do not provision or manage servers (A is incorrect). Lambda has a maximum execution time of 900 seconds (15 minutes), so functions cannot run indefinitely (D is incorrect).
   Further reading: [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

2. **B) The function is in a file named `app.py` with a method named `process_order`**
   The Lambda handler setting follows the format `file_name.function_name`. The part before the dot is the file name (without the `.py` extension), and the part after the dot is the function name within that file. So `app.process_order` means Lambda looks for a file named `app.py` and calls the `process_order` function inside it.
   Further reading: [Define Lambda function handler in Python](https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html)

3. **True.**
   The Lambda execution environment lifecycle has three phases. The Init phase runs once when Lambda creates a new execution environment: it downloads the code, initializes the runtime, and runs initialization code outside the handler (such as importing libraries and creating SDK clients). The Invoke phase runs the handler function and repeats for each invocation that reuses the same environment. The Shutdown phase occurs after a period of inactivity. This is why placing initialization code outside the handler (connection reuse) improves performance on subsequent invocations.
   Further reading: [Understanding the Lambda execution environment lifecycle](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html)

4. **Sample answer:** SQS is a poll-based event source, not a push-based trigger. Unlike S3 or SNS (which invoke Lambda directly by calling the Lambda API), SQS does not push messages to Lambda. Instead, you create an event source mapping that configures Lambda to poll the SQS queue for new messages at regular intervals. When messages are available, Lambda retrieves a batch and invokes your function with that batch. The permission model also differs: for push-based triggers like S3, the source service needs a resource-based policy on the function to invoke it. For poll-based sources like SQS, the function's execution role needs permission to read from the queue (such as `sqs:ReceiveMessage`, `sqs:DeleteMessage`, and `sqs:GetQueueAttributes`).
   Further reading: [Lambda event source mappings](https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html), [Using Lambda with Amazon SQS](https://docs.aws.amazon.com/lambda/latest/dg/services-sqs-configure.html)

5. **B) The function fails with an `AccessDeniedException` because the execution role lacks DynamoDB permissions**
   The `AWSLambdaBasicExecutionRole` managed policy only grants permission to write logs to CloudWatch Logs. It does not include any DynamoDB permissions. When the function attempts to call `dynamodb:GetItem`, the AWS SDK uses the temporary credentials from the execution role, and IAM denies the request because the role has no DynamoDB policy attached. The developer must add a permissions policy granting the specific DynamoDB actions needed (such as `dynamodb:GetItem`) on the specific table resource.
   Further reading: [Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html), [AWS managed policies for Lambda](https://docs.aws.amazon.com/lambda/latest/dg/permissions-managed-policies.html)

6. **B) Lambda encrypts environment variables at rest using AWS Key Management Service (AWS KMS)**
   Lambda automatically encrypts all environment variables at rest using an AWS KMS key. By default, Lambda uses an AWS managed key, but you can configure a customer managed key for additional control. Environment variables can be updated at any time through the console, CLI, or API (C is incorrect). They are scoped to a single function, not shared across an account (D is incorrect).
   Further reading: [Working with Lambda environment variables](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html)

7. **B, C**
   The slow requests are cold starts, which occur when Lambda creates a new execution environment after a period of inactivity. Provisioned concurrency (B) pre-initializes a specified number of execution environments so they are always warm, eliminating cold starts for those environments. Reducing the deployment package size (C) decreases the time Lambda needs to download and extract the code during the Init phase, directly reducing cold start duration. Increasing the timeout (A) does not reduce latency; it only prevents premature termination. Setting reserved concurrency to zero (D) would prevent the function from running at all. Java typically has longer cold starts than Python (E is incorrect).
   Further reading: [Configuring provisioned concurrency](https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html), [Understanding the Lambda execution environment lifecycle](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html)

8. **B) Lambda Layers**
   Lambda Layers let you package libraries, custom runtimes, and shared code separately from your function code. You create a layer containing the shared library, publish it as a versioned layer, and attach it to each function that needs it. When the library is updated, you publish a new layer version and update the functions to reference it, without redeploying the function code itself. Aliases (A) are for managing function versions, not shared dependencies. Extensions (C) are for integrating monitoring and security tools. Destinations (D) are for routing asynchronous invocation results.
   Further reading: [Managing Lambda dependencies with layers](https://docs.aws.amazon.com/lambda/latest/dg/chapter-layers.html)

9. **B) The Lambda function's response does not include the required `statusCode`, `headers`, and `body` fields in the format expected by API Gateway proxy integration**
   When using Lambda proxy integration, API Gateway expects the Lambda function to return a response object with specific fields: `statusCode` (integer), and optionally `headers` (object) and `body` (string). If the response format is incorrect, API Gateway cannot parse it and returns a generic "Internal server error" (HTTP 500) to the client. The function works when invoked directly because the Lambda service does not enforce this response format; only API Gateway requires it. If the API had not been deployed to a stage (C), the invoke URL would not exist at all. If the execution role lacked invoke permission (D), the error would be a 403 Forbidden, not a 500.
   Further reading: [Lambda proxy integrations in API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html), [Invoking a Lambda function using an Amazon API Gateway endpoint](https://docs.aws.amazon.com/lambda/latest/dg/services-apigateway.html)

10. **B) The number of invocations and the total GB-seconds of compute time (duration multiplied by memory)**
    Lambda pricing has two main components: a per-request charge (based on the number of invocations) and a duration charge (based on the total compute time measured in GB-seconds). GB-seconds are calculated by multiplying the memory allocated to the function (in GB) by the execution duration (in seconds). For this workload: 5 million requests at the per-request rate, plus (5,000,000 invocations x 0.2 seconds x 0.25 GB) = 250,000 GB-seconds at the duration rate. Deployment package size, Lambda Layers, `/tmp` storage, and CloudWatch log volume do not factor into the core Lambda compute cost (though CloudWatch Logs and API Gateway have their own pricing).
    Further reading: [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
