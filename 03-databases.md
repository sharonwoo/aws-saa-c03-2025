# databases

## rds + aurora

[rds overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528150#lecture-article)
* managed database service that uses SQL as query language
* ***it's basically AWS doing stuff on EC2 for you. storage is EBS***
* features: 
    * Fully automated provisioning of the database.
    * **Automated patching** of the underlying operating system.
    * **Continuous backups** with the ability to restore to a specific timestamp, known as Point in Time Restore.
    * Monitoring dashboards to view database performance.
    * Support for read replicas to improve read performance.
    * Multi-AZ deployments for disaster recovery.
    * Maintenance windows for upgrades.
    * Scaling capabilities both vertically (increasing instance type) and horizontally (adding read replicas).
    * Storage backed by Elastic Block Store (EBS).
* [exam topic] storage autoscaling - with no downtime or manual intervention: 
    * Free storage is less than 10% of the allocated storage.
    * The low storage condition persists for more than five minutes.
    * At least six hours have passed since the last storage modification.
    * **has to be manually enabled** (in prod i did not do this) and the max storage threshold set

[read replicas vs multi az](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078177#lecture-article)
* read replicas:
    * up to 15 read replicas
    * can be located same AZ, different AZ, different region (unusual) 
    * async replication - *eventually consistent*
    * e.g. analytics run on a read replica, isolated from the prod application
    * **note that prod must update its connection string** else it might miss a replica
    * network cost: read replicas within same region, no fee; **different region got fee**
* rds multi az: 
    * use case: disaster recovery
    * application reads/writes to master DB instance in AZ A, **synchronous** replication to standby instance in AZ B. cannot read or write directly to the standby in normal conditions...
    * **when the application writes to the Master, the change must also be replicated to the standby before it is accepted**
    * a single DNS name for the database
    * failure with the Master, there is an **automatic failover** to the standby database
* [exam topic] possible to configure read replicas as multi-az for disaster recovery. **click on modify for the database to enable it with zero downtime**. 
    * db snapshot taken, then made into its own standby instance thru restore. 

[rds hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528154#lecture-article)
* clickops sucks (and yes i have been there a bunch of times)
* The instance configuration determines the size of the underlying EC2 instance. Options include standard, memory optimized, or burstable classes.
* if no want AWS to handle networking for you (e.g. its used in EKS), must specify a VPC, subnet group, and whether public access is required. Public access is enabled to allow access from a personal computer
* make sure endpoint, port, security group and make sure all is in order to connect

[rds custom](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878670#lecture-article)
* oracle and microsoft sql server
* only 2 which have access to os and database customisation, access underlying EC2 instance using SSH etc
* recommended to deactivate automation mode when making any changes

[aurora](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528160#lecture-article)
* its compatible with postgres / mysql and supposed to be better
* automatic storage autoscaling
* up to 15 read replicas (faster replication), instant failover like less than 30 seconds, much faster than multi-az failover on mysql rds, high availability by default
*  four out of six copies (6 copies stored across 3 AZs) to acknowledge writes, so if one AZ goes down, writes can continue. For reads, it requires three out of six copies, ensuring high availability for read operations. includes a self-healing process that repairs corrupted or bad data through peer-to-peer replication in the backend
* 20% more cost than other dbs on rds
* writer endpoint, which is a DNS name that always points to the current master instance; reader endpoint, load balancing via the reader endpoint happens at the connection level, not at the individual statement level

[aurora hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528162#lecture-article)
* note, io optimised is more $ and performant
* set min and max ACU
* clickops lets you add readers, cross region read replica, replica auto scaling (scaling policy)
* the reader and writer endpoints are basically handy for applications

[advanced aurora](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099176#lecture-article)
* Replica Auto Scaling in Amazon Aurora automatically adds replicas to distribute read traffic and reduce CPU usage.
* Custom Endpoints allow targeting specific subsets of Aurora instances for specialized workloads, such as analytical queries.
* Aurora Serverless provides automated database instantiation and auto-scaling based on actual usage, ideal for intermittent or unpredictable workloads (**pay per second**)
* **Aurora Global Database** enables cross-region replication with low latency and fast disaster recovery, supporting up to five secondary regions and multiple read replicas.
* Aurora **integrates with AWS machine learning services** like SageMaker and Comprehend to provide ML-based predictions via SQL queries. 
* Babelfish for Aurora PostgreSQL allows applications using Microsoft SQL Server's T-SQL to communicate with Aurora PostgreSQL with minimal code changes, simplifying migrations.

[rds backups](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878680#lecture-article)
* auto: daily full backups, every 5 mins a transaction log backup, 1-35 days of retention, 0 to disable
* manual db snapshots, **never expire**
* save costs by snapshot and restore, instead of stopping the db
* aurora backups cannot be disabled, but rest are the same
* can restore mysql rds db from s3 (handy for onprem); aurora cluster takes more steps for restore (Percona XtraBackup)
* Aurora supports database cloning using copy-on-write, enabling fast and cost-effective creation of staging environments without impacting production.

[rds & aurora security](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528156#lecture-article)
* can encrypt data at rest -- **must be defined at launch**
* to encrypt existing, snapshot, then encrypt, then restore as encrypted
* **in flight encryption**
* authentication: iam vs username+password
* security groups as always
* audit logs to track database activity (only temp storage), for long term send to cloudwatch logs

[rds proxy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878690#lecture-article)
* fully managed database proxy - pool and share DB connections, improving db efficiency and minimise open connections, reduces failover time by up to 66% by managing failover internally
* serverless, autoscaling, highly available
* only IAM - IAM authentication with credentials stored securely in AWS Secrets Manager, accessible only within VPC. apparently good with lambda

[elasticache](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528166#lecture-article)
* managed redis/memcached - in memory db with high performance
* reduce load for read intensive workloads, makes application stateless (?)
* heavy application code changes - query cache before query db... 
    * e.g. using elasticache for user sessions
    * more problems with cache invalidation and consistency
* redis vs memcached: redis has high availability and replication, memcached does not; memcached is multithreaded

[hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528168#lecture-article)
* design your own cache; can restore from backup, or use easy create, or configure everything manually
* cluster mode: disabled (single shard, up to 5 read replicas), or enalbed
* naming and location (can be on prem using aws outposts)
* multi-az needs to be enabled 
* engine version, ports, parameter, node type 
* replicas and scaling
* create subnet group or use existing

[elasticache security](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528170#lecture-article)
* iam for redis only... password/token upon creation is extra security
* memcached has SASL based auth
* elasticache patterns: lazy loading, write through, session store
* redis sorted sets - very popular for e.g. gaming leaderboards

[remember these ports for exam](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528170#lecture-article): 
* FTP: 21
* SSH: 22
* SFTP: 22 (same as SSH)
* HTTP: 80
* HTTPS: 443
* PostgreSQL: 5432
* MySQL: 3306
* Oracle RDS: 1521
* MSSQL Server: 1433
* MariaDB: 3306 (same as MySQL)
* Aurora: 5432 (if PostgreSQL compatible) or 3306 (if MySQL compatible)

## storage in aws - review section

[choosing the right db](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528430#learning-tools)
* Choosing the right database depends on workload characteristics such as read/write balance, data size, growth, and latency requirements.
* Consider data model, query needs, schema flexibility, and whether relational or NoSQL databases are appropriate.
* AWS offers a variety of managed databases including RDBMS (RDS, Aurora), NoSQL (DynamoDB, ElastiCache, Neptune, DocumentDB, Keyspaces), object stores, data warehousing (Redshift, Athena, EMR), search (OpenSearch), graph (Neptune), ledger (QLDB), and time series (Timestream).

[rds review lecture](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528402#lecture-article)

[aurora AGAIN](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528406#lecture-article) - this is better than the above ugh
* Aurora Serverless: Designed for unpredictable and intermittent workloads, this feature eliminates the need for capacity planning.
* Aurora Global Database: Supports up to 16 read instances per region across replicated regions. Storage replication typically occurs in less than one second across regions, which is important for exam considerations. In case of failure in the primary region, a secondary region can be promoted to primary.
* Aurora Machine Learning: Enables integration with SageMaker and Comprehend for machine learning capabilities on top of Aurora.
* Aurora Database Cloning: Allows creation of a new Aurora cluster from an existing one for testing or staging purposes. This process is significantly faster than creating a snapshot and restoring it.

[elasticache](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528412#lecture-article)

[dynamo](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528412#lecture-article)
* Amazon DynamoDB is a managed, serverless, NoSQL database offering millisecond latency.
* *two capacity modes: provisioned capacity with optional auto scaling, and on-demand capacity for unpredictable workloads.
* DynamoDB supports high availability across multiple availability zones and provides features like transactions, TTL for expiring data, and **DAX for microsecond read latency (read cache)**.
* Integration with AWS services includes IAM for security, DynamoDB Streams for event processing with Lambda, and Kinesis Data Streams for extended data retention and processing.
* **Global tables** enable active-active replication across multiple regions.
* Backup options include point-in-time recovery for up to 35 days and on-demand backups for longer retention.
* DynamoDB supports exporting and importing tables to and from Amazon S3 without consuming read capacity units.
* It is ideal for serverless applications requiring flexible schema and small document storage or as a distributed serverless cache.

[s3](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13672438#lecture-article)
* great for big objects, infinite scaling
* storage tiers - transition between with lifecycle policies

[documentdb](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878844#lecture-article) - basically aws mongo
* compatible with mongo
* same deployment characteristics as aurora

[neptune](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13672456#lecture-article)
* graph db option
* **neptune streams** - real time ordered sequence of changes (basically a db log)

[keyspaces](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878864#lecture-article) - aws cassandra

[qldb](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878866#lecture-article) vs managed blockchain
* QLDB and Managed Blockchain is that QLDB does not have decentralization; QLDB is a central database owned by Amazon that maintains the journal
* any time a modification is made, a crypto hash is computed
* SQL compatible... lol

[timestream zzzz](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878868#overview) - sql compatible