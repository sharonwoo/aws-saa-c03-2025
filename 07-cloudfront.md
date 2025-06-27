# cloudfront

AWS's content delivery network

[cloudfront overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528370#lecture-article)
* 216 points of presence - aws edge locations (continually being added to)
* DDOS protection - enhanced by aws shield and web application firewall
* **cloudfront origins**
    * s3 buckets: distributing files and caching them at the Edge, also can be used for uploading. connection secured using **origin access control**
    * vpc origins: can connect directly to private subnet resources with cloudfront
    * custom origins: any http backend (including hosted outside aws)
* request flow: 
    * edge location checks if content cached locally, if not cached then retrieve it from origin
    * files cached typically for about a day, in each location
* vs s3 cross region replication
    * s3 is realtime, no caching, ideal for dynamic content

[cloudfront with s3 hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528374#lecture-article)
* **create distribution**: choose an origin and a path; set origin access (public, oac, legacy), create oac if have to. set default root object. edit bucket policy, [aws gives you smth to copypaste](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html). access any file in bucket as long as origin matches the one that's set
* copy distribution name~

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Sid": "AllowCloudFrontServicePrincipalReadWrite",
        "Effect": "Allow",
        "Principal": {
            "Service": "cloudfront.amazonaws.com"
        },
        "Action": [
            "s3:GetObject",
            "s3:PutObject"
        ],
        "Resource": "arn:aws:s3:::<S3 bucket name>/*",
        "Condition": {
            "StringEquals": {
                "AWS:SourceArn": "arn:aws:cloudfront::111122223333:distribution/<CloudFront distribution ID>"
            }
        }
    }
}
```

[cloudfront alb or ec2 as origin](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/34844034#lecture-article) - vpc origins
* basically punch to internet while subnet remains private, same as lambda lolz
* previous way used an ec2 instance in public subnet, allow traffic thru the ec2 instance to the alb. manual

[cloudfront geo restriction](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/34844038#lecture-article)
* allowlist or blocklist
* 3rd party geo-ip database
* it's under security and the web application firewall in the console

[cloudfront price classes](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878736#lecture-article)
* pricing varies for egress per region
* Price Class All: Includes all regions, providing the best performance but at a higher cost. For example, edge locations in India cost more than those in the United States.
* Price Class 200: Includes most regions but excludes the most expensive ones.
* Price Class 100: Includes only the least expensive regions.

[cloudfront cache invalidation](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878742#lecture-article)
* when origin updated, cloudfront won't know until TTL expires. so force a partial cache refresh thru cloudfront invalidation
* either wildcard or specific paths

[global accelerator](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078277#lecture-article)
* goal is to route traffic as quickly as possible through the AWS global network to minimize latency using **anycast IP*, **provides two static Anycast IP addresses for your application globally**
* IPs route traffic to the closest edge location of your users, which then forwards the traffic through the private AWS network to your application, more stable and lower latency connection compared to the public internet
* supports various AWS resources including Elastic IPs, EC2 instances, Application Load Balancers, and Network Load Balancers, whether they are public or private
* includes health checks on application endpoints, will failover to healthy endpoint in less than a minute
* *only two external IP addresses need to be whitelisted by your clients* - quite secure, and automatic DDOS protection thru AWS shield
* **no caching**: Global Accelerator improves performance for a wide range of applications over TCP or UDP by *proxying packets from edge locations to applications running in one or more AWS regions*
* **unicast ip**: 1:1 server to unique IP address
* **anycast ip**: multiple servers *share the same IP address*; network routes the client to the nearest server holding that IP

[global accelerator hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078279#lecture-article)
* no free tier... sibei expensive. fixed hourly fee for each accelerator running in your account, approximately $0.025 per hour + data transfer fees (but smol, like less than a buck per gib)