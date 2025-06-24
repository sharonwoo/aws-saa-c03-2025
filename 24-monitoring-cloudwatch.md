# monitoring: cloudwatch, cloudtrail, eventbridge

note: the lecture article is really handy, should probably extract those

[cloudwatch metrics](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13723108#lecture-article):
* **metric** - any variable that can be monitored. 
* one **namespace** per aws service. e.g. ec2 metrics per instance
* each metric can have **dimensions** e.g. instance id, environment, up to 30 of these per metric
* timestamped
* can create custom metrics
* can create cloudwatch dashboards of metrics
* stream metrics to destination of choice e.g. kinesis data firehose, datadog
* default frequency is 5 minute intervals, detailed monitoring is 1 minute intervals

[cloudwatch logs](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13723104#lecture-article)
* **log groups** usually represent a single application. e.g. 1 lambda: 1 log group; **log stream** at event granularity
* logs can be exported e.g. to s3 (**batch**, takes up to 12 hours), streamed into kinesis data streams, data firehose (**cloudwatch logs subscription**). can aggregate logs across accounts and regions into 1 destination
* all logs encrypted by default and can configure own kms based enctryption with custom keys
* sources: SDK, cloudwatch logs agent, cloudwatch unified agent
* direct from specific services: elastic beanstalk, ecs, lambda, vpc flow, api gateway, cloudtrail, route53
* **cloudwatch logs insights**: query engine for cloudwatch logs, [e.g.](https://ap-southeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-southeast-1#logsV2:logs-insights)

[cloudwatch logs hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/29322936#lecture-article)
* essentially a review of the earlier [cloudwatch logs](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13723104#lecture-article)
* **metric patterns** are string search (looks a lot like regex)
* **subscription filters** allow you to forward log data to other AWS services for further processing. Supported destinations include Elasticsearch, Kinesis Data Streams, Kinesis Firehose, and Lambda functions. You can create up to two subscription filters per log group. This feature enables integration of log data with custom analytics or processing pipelines.

[cloudwatch livetail](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/41865670#lecture-article)
* it's new, it's probably shoehorned in to get $, can't you just do this in the cmd line? lol

[cloudwatch agent and logs agent](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/21411450#lecture-article)
* ec2 instance doesn't automatically send logs to cloudwatch; to enable functionality, need to run **cloudwatch agent** on ec2 to push whatever log files needed, also need **iam role** allowing logs to be sent to cloudwatch logs
* new agent: **cloudwatch unified agent**, which is much more granular than old one and can be configured using ssm parameter store (centralised config for agents)
* old agent: **cloudwatch logs agent**

[cloudwatch alarms](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528438#lecture-article)
* alarm states: 
  *  **OK**: The alarm is not triggered.
  *  **INSUFFICIENT_DATA**: There is not enough data for the alarm to determine a state.
  *  **ALARM**: The threshold has been breached, and a notification will be sent.
* alarm evaluation period
* alarm targets: 
  * EC2 Instance Actions: Such as stopping, terminating, rebooting, or recovering an instance.
  * Auto-Scaling Actions: For example, scaling out or scaling in.
  * Notifications: Sending alerts to the SNS service, which can trigger Lambda functions to perform various tasks based on the alarm breach.
* **composite alarms** allow alarms to be combined with logics e.g. AND / OR 
* can create alarms off cloudwatch logs metric filters (send msg to amazon sns if error message pops up in logs...)
* To test alarms and notifications, you can use the CLI command set-alarm-state. This allows you to trigger an alarm manually, even if the threshold has not been reached, to verify that the alarm triggers the correct actions in your infrastructure.

[cloudwatch alarms hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/27548328#lecture-article)
* mostly ec2 instance alarm based on cpu utilisation

[eventbridge](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/27548342#lecture-article)
* FANCY CRON!!! not only an event schedule, but an event pattern + triggers.
* default event bus is for aws services, but also **partner event bus** or **custom event bus**
* archive events (bespoke period / indefinite)
* eventbridge schema registry - analyse events and infer the schema. schema can be versioned.
* resource based policy, manage permissions for a specific event bus (regions/accounts), e.g. central event bus for all accounts in an organisation
* default timezone is UTC
* will automatically retry with exponential backoff

[eventbridge hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/27548348#lecture-article)
* default event bus: https://ap-southeast-1.console.aws.amazon.com/events/home?region=ap-southeast-1#/eventbuses, created upon account creation. can create more event buses with tf/cli etc. cross account access definition in resource-based policy.
* can clickops the partner event buses (under integration tab)
* create rule - either event pattern, or schedule
* create a sample event in UI, create test events. aws provides some examples already in the ui. test event rule against their sample events. **schema registry**, more of such sample events. download code bindings aka templates that already manipulate these events in java, python etc.

[cloudwatch container insights](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878930#lecture-article)
* **container insights** - ECS, EKS, EC2 k8s, fargate (for both ecs/eks). buggy af. needs the agent for kubernetes
* **lambda insights** - cold starts, shutdowns, system level metrics. provided as a lambda layer
* **contributor insights** - for logging by user. any logs generated by aws. e.g. vpc flow logs - analyse for top 10 ip addresses. aws has already created sample rules, built in rules for metrics
* **application insights** - for anything on EC2 instances. automatic dashboard

[cloudtrail overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099076#lecture-article)
* enabled by default, provides governance, compliance and audit over events/api calls within the account. if resource is deleted in aws, check in cloudtrail
* kept for 90 days, after which put in s3 bucket then use athena to analyse
* 3 types of events
  * **management events** - read or write events, e.g. aws cli list all ec2 instances
  * **data events** - by default are not logged. aws s3 get object etc
  * **cloudtrail insights events** - have to be enabled, have to pay. detect unusual activity. analyse normal events to createa. baseline and then looks at write events to see if anything is anomalous, insight events appear in *cloudtrail console*, can send to s3 or eventbridge event

[cloudtrail hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099064#lecture-article)
* examples of integration

[aws config](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078369#lecture-article)
* service that allows you to audit and record the compliance of your resources in AWS based on rules that you set, and receive alerts. 
* **per-region service** so needs to be configured appropriately. rules can be stored in s3
* aws config rules, or create your own rules
* sibei expensive. no free tier. 0.3 cents per config recorded, 0.1 cent per config rule eval
* use eventbridge to trigger notifs when aws resources are not compliant

[aws config hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078373#lecture-article)
* clickops: can default record all resources supported in this region (lol), check include global resources e.g. iam. create a bucket to store the logs in the bucket. default has 100+ managed rules... 
* example: check if any ec2 security groups have ingress rules, can see which security groups are compliant; delete rule and then retrigger the rule. resource timeline, shows when things become compliant or not
* define remediation actions for rule

[cloudtrail vs cloudwatch vs config](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078375#lecture-article)
* cloudwatch is for performance monitoring, + events, + log aggregation and analysis
* cloudtrail to record api calls
* config is to record config changes and eval if configs are within rules