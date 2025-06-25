# databases

## rds + aurora

[rds overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528150#lecture-article)
* managed database service that uses SQL as query language
* it's basically AWS doing stuff on EC2 for you. storage is EBS
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

[read replicas vs multi az](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078177#lecture-article)
* read replicas:
    * up to 15 read replicas
    * can be located same AZ, different AZ, different region (unusual) 
    * async replication - *eventually consistent*
    * 