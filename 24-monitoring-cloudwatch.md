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