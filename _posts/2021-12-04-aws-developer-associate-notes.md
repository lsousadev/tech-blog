---
layout: post
author: Lucas
title: AWS Developer Associate Certification Notes
subtitle: Note Repository
categories: [aws]
tags: [aws, cloud, certificate, aws developer associate]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## API Gateway

- Error codes:
  - 403: Access denied (authentication or authorization issues)
  - 429: Limit exceeded or too many requests (throttling)
  - 502: Incompatible output
  - 504: INTEGRATION_FAILURE or INTEGRATION_TIMEOUT (default = 29s for all integration types)
- Lambda authorizers:
  - an API Gateway feature that uses a Lambda function to control access to your API
  - takes the caller's identity as input and returns an IAM policy as output
  - token-based: eceives the caller's identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token
  - request parameter-based: eceives the caller's identity in a combination of headers, query string parameters, stageVariables, and $context variables
- Non-proxy Lambda integration:

![API Gateway Lambda Integration](/assets/images/api_gateway_lambda_integration.png)

## CloudFormation

- StackSets extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and regions with a single operation. Using an administrator account, you define and manage an AWS CloudFormation template, and use the template as the basis for provisioning stacks into selected target accounts across specified regions.
- Under the `AWS::Lambda::Function` resource, you can use the Code property which contains the deployment package for a Lambda function. For all runtimes, you can specify the location of an object in Amazon S3 (`S3Key` and `S3Bucket`). For Node.js and Python functions, you can specify the function code inline in the template using `ZipFile`.
- The existing Parameters section of a template to define Systems Manager parameters. Systems Manager parameters refer to actual values in the Parameter Store. The value for this type of parameter would be the Systems Manager (SSM) parameter key instead of a string or other value. CloudFormation will fetch values stored against these keys in Systems Manager in your account and use them for the current stack operation.
  - As parameters are updated in Systems Manager, you can have the new value of the parameter take effect by just executing a stack update operation.

## CloudWatch

- Alarms:
  - Period: length of time in seconds to evaluate each data point
  - Evaluation period: # of most recent periods/data points to evaluate
  - Datapoints to alarm: how many breached data points needed in evaluation period to set state to ALARM
- CloudWatch does not monitor the memory, swap, and disk space utilization of your instances.
- Monitoring:
  - Basic: 5 minutes, N/A for ELB and RDS
  - Detailed: 1 minute,  N/A for EBS
  - Custom: install CloudWatch agent and configure it to send custom metrics.
    - Standard resolution: 1 minute
    - High resolution: down to 1 second
  - Enhanced: Real time metrics for RDS. CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance. In contrast, Enhanced Monitoring gathers its metrics from an agent on the DB instance.
- RDS logs available via DB parameters:
  - Audit log
  - Error log (default)
  - General log
  - Slow query log
- Other definitions:
  - Dimension: name/value pair that is part of the identity of a metric
  - Metric Math: used to query or create time series based on multiple multiple metrics
  - Namespace: grouping of metrics aggregated together

## CodeBuild

- Fully managed build service that compiles source code, runs tests, and produces software packages that are ready to deploy

## CodeCommit

- Credentials: must have permissions to access AWS resources (such as CodeCommit repositories) and your IAM user, used to manage your Git credentials or the SSH public key for Git connections. With HTTPS connections and Git credentials, you generate a static user name and password in IAM. You then use these credentials with Git and any third-party tool that supports Git user name and password authentication.

## CodeDeploy

- AppSpec `hooks`:
  - EC2/on-prem: mappings that link deployment lifecycle event hooks to one or more scripts
  - Lambda/ECS: specifies Lambda validation functions to run during a deployment lifecycle event. An AWS Lambda hook is one Lambda function specified with a string on a new line after the name of the lifecycle event. Each hook is executed once per deployment. Two options: `BeforeAllowTraffic` and `AfterAllowTraffic`
- CodeDeploy Agent:
  - software package that, when installed and configured on an instance, makes it possible for that instance to be used in CodeDeploy deployments
  - communicates outbound using HTTPS over port 443
  - only needed for EC2/on prem
- Automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services
- Deployment options:
  - In-place: app is stopped, new version is installed, started, and validated. Only for EC2/on-prem
  - Blue/Green:
    - EC2: original environment is replaced by a different set of instances
    - Lambda: only option. Traffic shifted between versions. Functions for testing can be specified. Way traffic shift happens can be chosen.
    - ECS: Traffic is shifted from the task set with the original version of a containerized application in an ECS service to a replacement task set in the same service. The protocol and port of a specified load balancer listener are used to reroute production traffic. During deployment, a test listener can be used to serve traffic to the replacement task set while validation tests are run.

## Cognito

- User Pools: just a user directory
- Sync: a client library that enables cross-device syncing of application-related user data

## DynamoDB

- Expressions:
  - Condition Expression: condition for attribute values to select items to be modified
  - Filter Expression: filters whole items off of fetched results before returning result
  - Projection Expression: fetch only specified attributes from items
- Errors:
  - `ProvisionedThroughputExceededException` - more TPUT (WCU or RCU) used than provisioned
  - `RequestLimitExceeded` - provisioned TPUT exceeds account TPUT limit
  - `ThrottlingException` - rate of requests exceeds allowed TPUT
- Operations:
  - Atomic counter: implemented via `UpdateItem`, increments a numeric attribute unconditionally, can be retried, can fail/duplicate
  - Conditional write: good for precise operations, no room for error
    - Optimistic Locking: record is locked only when changes are committed to the database. Depends on checking a value upon save to ensure that it has not changed.
    - Pessimistic Locking: record is locked while it is edited.
- Provisioned throughput math:
  - One read request unit represents one strongly consistent read request, or two eventually consistent read requests, for an item up to 4 KB in size
  - Transactional read requests require 2 read request units to perform one read for items up to 4 KB
- Secondary indexes:
  - Global: partition and sort key can both be different from base table. On a provisioned table, GSI’s have their own WCU and RCU. Contains a copy of some or all of the attributes from its base table; you specify which attributes are projected into the GSI when you create the index.
  - Local: must have same partition key as base table, different sort key. Called local because the index is made within the partition of the base table. Can only be created on table creation. Contains a copy of some or all of the attributes from its base table; you specify which attributes are projected into the LSI when you create the table.
- Streams:
  - Kinesis Adapter is recommended way to consume streams from DynamoDB for real-time processing. The DynamoDB Streams API is intentionally similar to that of Kinesis Streams
  - Lambda isn’t advised to consume DynamoDB Streams real-time data as it reads records in batches

## EBS

- New volumes are raw block devices and do not contain any partition or file system.
- Detaching an EBS volume from an EC2 instance:
  - terminate instance
  - unmount volume or stop instance, then detach
    - for root volumes, you must stop instance then detach

## EC2

- You can and should use an IAM role to manage temporary credentials for applications that run on an EC2 instance. The role supplies temporary permissions that applications can use when they make calls to other AWS resources. When you launch an EC2 instance, you specify an IAM role to associate with the instance. Applications that run on the instance can then use the role-supplied temporary credentials to sign API requests.
  - In order to assign a role to an EC2 instance, you must create an instance profile that is attached to the instance. The instance profile contains the role and can provide the role's temporary credentials to an application that runs on the instance.

## ECS

- Task placement strategy: algorithm for selecting instances for task placement or tasks for termination. Task placement strategies can be specified when either running a task or creating a new service.
  - binpack: Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.
  - random
  - spread: Place tasks evenly based on the specified value. Accepted values are instanceId, host, or a custom attribute key:value pairs such as `attribute:ecs.availability-zone`
    - Cluster queries are expressions that enable you to group objects. For example, you can group container instances by attributes such as Availability Zone, instance type, or custom metadata. You can add custom metadata to your container instances, known as attributes. Each attribute has a name and an optional string value. You can use the built-in attributes provided by Amazon ECS or define custom attributes.

## Elastic Beanstalk

- CLI
  - deployment: package app as `zip` file and deploy with `eb deploy`
  - `aws elasticbeanstalk update-application` doesn’t allow for package upload, only updates specified properties of the app
- Deployment methods (note that `canary` is not available):

![Elastic Beanstalk Deployment Methods](/assets/images/elastic_beanstalk_deployment_methods.png)

- Note that Canary is not available
- Environment: an instance of a version of an application. You can have multiple environments of the same version or app at the same time
  - Server environment: composed of an ELB and ASG of EC2 instances containing EB app that writes requests to SQS queue
  - Worker environment: composed of an ASG of EC2 instances containing EB app and an SQS daemon that pulls requests from EB SQS queue
- Files:
  - `cron.yaml` - configuration file primarily used to define periodic tasks that add jobs to your worker environment's queue automatically at a regular interval.
  - `Dockerrun.aws.json` - configuration file primarily used in multicontainer Docker environments hosted in Elastic Beanstalk. This can be used alone or combined with source code and content in a source bundle to create an environment on a Docker platform.
  - `env.yaml` - manifest in the root of your application source bundle to configure the environment name, solution stack and environment links to use when creating your environment. The manifest uses the same format as Saved Configurations.
- It isn’t good practice to spin up an RDS instance as part of your EB app as it ties the lifecycle of the database to the lifecycle of app environment. It is better to decouple it by creating an RDS instance separately and configure the app with the necessary information to connect on launch.
  - Note that a sec. group cannot be deleted if it is linked to another sec. group by a rule.
- Platform Version: New platform versions provide updates to existing software components and support for new features and configuration options. Eg: Java 7 to Java 8
  - Two methods to update platform version:
    - Update your Environment's Platform Version - This is the recommended method when you're updating to the latest platform version, without a change in runtime, web server, or application server versions, and without a change in the major platform version. This is the most common and routine platform update.
    - Blue/Green Deployment - This is the recommended method when you're updating to a different runtime, web server, or application server versions, or to a different major platform version. This is a good approach when you want to take advantage of new runtime capabilities or the latest Elastic Beanstalk functionality.
- Usage: create an application, upload an application version in the form of an application source bundle (for example, a Java .war file) to Elastic Beanstalk, and then provide some information about the application. Elastic Beanstalk automatically launches an environment and creates and configures the AWS resources needed to run your code. After your environment is launched, you can then manage your environment and deploy new application versions.

## Elasticache

- Types:
  - Redis: rich features. Blazing fast in-memory data store that provides sub-millisecond latency to power internet-scale real-time applications. Redis also lets you create multiple replicas of a Redis primary. This allows you to scale database reads and to have highly available clusters.
  - Memcached: simpler. Less available but can provide multiple threads or cores, whereas Redis is mostly a single-threaded server
- Strategies:
  - Lazy Loading: write to cache on cache miss
  - Russian Doll: nested records with top level cache keys (eg. user, stories, comments)
  - Write Through: write to cache on write to database

## Kinesis Data Streams

- Duplication of data: usually caused by producer or consumer retries, can be mitigated by adding a primary key to the record
- KCL worker (EC2 instance) can process any number of shards, but a shard can only have one record processor (KCL worker)

## Kinesis Data Firehose

- The easiest way to load streaming data into data stores and analytics tools.
- It is a fully managed service that automatically scales to match the throughput of your data.
- It can also batch, compress, and encrypt the data before loading it.

## Kinesis Data Analytics

- Analyze streaming data, gain actionable insights, and respond to your business and customer needs in real time. You can quickly build SQL queries and Java applications using built-in templates and operators for common processing functions to organize, transform, aggregate, and analyze data at any scale.

## KMS

- Envelope encryption is the practice of encrypting plaintext data with a data key and then encrypting the data key with another top-level master key.
  - Encrypting data locally:
        1. Use the `GenerateDataKey` operation to get a data encryption key.
        2. Use the plaintext data key (returned in the `Plaintext` field of the response) to encrypt data locally, then erase the plaintext data key from memory.
        3. Store the encrypted data key (returned in the `CiphertextBlob` field of the response) alongside the locally encrypted data.
  - Decrypting data locally:
        1. Use the `Decrypt` operation to decrypt the encrypted data key. The operation returns a plaintext copy of the data key.
        2. Use the plaintext data key to decrypt data locally, then erase the plaintext data key from memory.

## Lambda

- CPU: In the AWS Lambda resource model, you choose the amount of memory you want for your function and are allocated proportional CPU power and other resources. An increase in memory size triggers an equivalent increase in CPU available to your function.
- Concurrency for push-based event sources and invocations:
  - concurrent executions = (invocations per second) x (average execution duration in seconds)
  - default concurrency limit = 1000/region
    - minimum unreserved concurrency per region = 100 (ie. you can reserve 900 between your functions, or one function with 900)
- Concurrency for poll-based event source (Kinesis) equals the number of shards.
- Execution context: provides 512 MB of additional disk space in the `/tmp` directory. The directory content remains when the execution context is frozen, providing transient cache that can be used for multiple invocations. You can add extra code to check if the cache has the data that you stored.
- Layer: a ZIP archive that contains libraries, a custom runtime, or other dependencies
- InvocationType:
  - RequestResponse (Synchronous): default. Connection stays open until function returns something or times out. API response includes the function response
  - Event (Asynchronous): events that fail multiple times can be sent to DLQ and API response only includes status code
  - Dry-run: validate parameter values and invocation permissions
- Runtimes supported: PowerShell, Go, Python, Node.js, Java, C#, Ruby, Custom for everything else
- Traffic Shifting: the `routing-config` parameter of a Lambda alias can be implemented for that alias to point to two different versions of a function, as long as they have same IAM exec. role, DLQ config, and none of the versions are $LATEST

## S3

- CORS config document’s `<CORSrule>` components:
  - `AllowedOrigin` - Specifies domain origins that you allow to make cross-domain requests.
  - `AllowedMethod` - Specifies a type of request you allow (GET, PUT, POST, DELETE, HEAD) in cross-domain requests.
  - `AllowedHeader` - Specifies the headers allowed in a preflight request.
  - `MaxAgeSeconds`  - Specifies the amount of time in seconds (in this example, 3000) that the browser caches an Amazon S3 response to a preflight OPTIONS request for the specified resource. By caching the response, the browser does not have to send preflight requests to Amazon S3 if the original request will be repeated.
  - `ExposeHeader`  - Identifies the response headers (e.g. `x-amz-server-side-encryption`, `x-amz-request-id`, and `x-amz-id-2`) that customers are able to access from their applications (e.g. from a JavaScript `XMLHttpRequest` object).
- Cross-region replication (CRR) enables automatic, asynchronous copying of objects across buckets in different AWS Regions. Buckets configured for cross-region replication can be owned by the same AWS account or by different accounts. Cross-region replication is enabled with a bucket-level configuration. You add the replication configuration to your source bucket.
  - Requirements:
    - The source and destination buckets must have versioning enabled.
    - The source and destination buckets must be in different AWS Regions.
    - Amazon S3 must have permissions to replicate objects from that source bucket to the destination bucket on your behalf.
- Encryption:
  - SSE-S3 - header: `x-amz-server-side-encryption` (example value: `AES256`)
  - SSE-KMS
    - headers:
      - `x-amz-server-side-encryption` (value `aws:kms`)
      - `x-amz-server-side-encryption-aws-kms-key-id` (optional, defaults to default CMK for region, which is created automatically by S3)
  - SSE-C: customer-provider encryption keys
    - headers:
      - `x-amz-server-side-encryption-customer-algorithm`
      - `x-amz-server-side-encryption-customer-key`
      - `x-amz-server-side-encryption-customer-key-MD5`
- Glacier object retrieval:
  - Standard: 3-5 hours
  - Bulk: 5-12 hours
  - Expedited: 1-5 minutes
- Multipart Upload: when using a KMS customer master key (CMK), the requester must have permission to the `kms:Decrypt` and `kms:GenerateDataKey*` actions on the key. These permissions are required because Amazon S3 must decrypt and read data from the encrypted file parts before it completes the multipart upload.
- S3 Transfer Acceleration leverages Amazon CloudFront’s globally distributed AWS Edge Locations. As data arrives at an AWS Edge Location, data is routed to your Amazon S3 bucket over an optimized network path.

## SAM

- Consists of the AWS SAM template specification that you use to define your serverless applications, and the AWS SAM command line interface (AWS SAM CLI) that you use to build, test, and deploy your serverless applications. The CLI provides a Lambda-like execution environment locally. It helps you catch issues upfront by providing parity with the actual Lambda execution environment.

## SQS

- Delay queue lets you postpone the delivery of new messages to a queue for a number of seconds. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes
- Long polling is a way to retrieve messages from your Amazon SQS queues. While the regular short polling returns immediately, even if the message queue being polled is empty, long polling doesn’t return a response until a message arrives in the message queue, or the long poll times out.

## STS

- API calls:
  - `AssumeRole` - needs a role ARN as parameter and returns the temporary credentials to a newly created session with same policies/access as the role.
  - `AssumeRoleWithSAML` - same as above but for users authenticated via SAML, eg. enterprise identity store or directory.
  - `AssumeRoleWithWebIdentity` - same as above but for federated users authenticated via public identity providers such as Google.
  - `GetFederationToken` - doesn’t support MFA.
  - `GetSessionToken` - returns set of temporary credentials for accounts and users only. The credentials consist of an access key ID, a secret access key, and a security token. Typically used for MFA.

## Systems Manager Parameter Store

- Provides secure, hierarchical storage for configuration data management and secrets management

## VPC

- Route tables:
  - default limit of 200 route tables per VPC
  - you can associate multiple subnets to the same route table
  - you cannot modify/edit the main route created by default
  - a subnet can only be associated with one route table at a time

## X-Ray

- Active Tracing: feature you can enable in Lambda
- Components:
  - Annotation: key-value pairs added to traces, segments, and subsegments and are indexed for filter expression searches
  - Metadata: same as annotation but not indexed
  - Sampling algorithm: default is 1st request of every second + 5% of remaining requests
  - Segment: provides the name of compute resource handling request, details about request and work done
    - host - host name, alias or IP address
    - request - method, client address, path, user agent
    - response - status, content
    - work done - start and end time, subsegments
    - issues - errors, faults, exceptions
  - Subsegment: additional details about work done
    - namespace: field in subsegment. `aws` for AWS SDK calls; `remote` for other downstream calls
    - http: http object with information about an outgoing HTTP call
    - aws: aws object with information about the downstream AWS resource that your application called.
    - annotation, metadata
    - error, throttle, fault, and cause
    - precursor_ids: array of subsegment IDs that identifies subsegments with the same parent that completed prior to this subsegment
  - Trace: string of segments generated by a single request
  - Tracing header: added in the HTTP request header (not on the segment document). A tracing header (`X-Amzn-Trace-Id`) can originate from the X-Ray SDK, an AWS service, or the client request
- ECS:
  - create a Docker image that runs the X-Ray daemon
  - upload it to a Docker image repository
  - deploy it to your Amazon ECS cluster
  - use port mappings and network mode settings in your task definition file to allow your application to communicate with the daemon container
    - the daemon is a software application that listens for traffic on UDP port 2000, gathers raw segment data, and relays it to the AWS X-Ray API
- Enabling X-Ray:
  - Elastic Beanstalk: include xray-daemon.config file in .ebextensions directory of source code or via console
  - ECS: create a docker image that runs the X-Ray daemon
- Environment variables used by Lambda to facilitate communication with the X-Ray daemon and configure the X-Ray SDK:
  - _X_AMZN_TRACE_ID: contains tracing header (sampling decision, tracing ID, parent segment ID)
  - AWS_XRAY_CONTEXT_MISSING: used by SDK to determine what to do if no tracing header. Set by Lambda to LOG_ERROR by default
  - AWS_XRAY_DAEMON_ADDRESS: formatted IP_ADDRESS:PORT and used to send trace data to daemon directly, without SDK
- If a load balancer or other intermediary forwards a request to your application, X-Ray takes the client IP from the `X-Forwarded-For` header in the request instead of from the source IP in the IP packet, which would only show the ELB’s IP address.
- SDK: does not send trace data directly to AWS X-Ray. To avoid calling the service every time your application serves a request, the SDK sends the trace data to a daemon, which collects segments for multiple requests and uploads them in batches.
