# cloudwatch

[cloudwatch metrics](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13723108#lecture-article):
* **metric** - any variable that can be monitored. 
* one **namespace** per aws service. e.g. ec2 metrics per instance
* each metric can have **dimensions** e.g. instance id, environment, up to 30 of these per metric
* timestamped
* can create custom metrics
* can create cloudwatch dashboards of metrics
* stream metrics to destination of choice e.g. kinesis data firehose, datadog
* default frequency is 5 minute intervals, detailed monitoring is 1 minute intervals