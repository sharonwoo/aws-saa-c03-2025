# route53

[what is dns](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/28384280#lecture-article)
* this is much better - [the jim kurose](https://www.youtube.com/watch?v=6lRcMh5Yphg)
* fully qualified domain name = http://api.www.example.com. http protocol, rest of it is the FQDN, `.com` is tld, `example.com` second level domain, `www.example.com` is a subdomain
* DNS translates human-friendly hostnames into IP addresses, enabling web browsers to access servers.
* DNS has a hierarchical naming structure including top-level domains, second-level domains, and subdomains.
* DNS resolution involves recursive queries starting from the root DNS server down to authoritative name servers.
* Local DNS servers cache responses to improve query efficiency and reduce latency.

[route53 overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528180#lecture-article) + [registering domain](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/28384290#lecture-article) + [records](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/28384312#lecture-article) + [ttl](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528188#lecture-article)
* for the exam: 
    * A: hostname to IPv4
    * AAAA: hostname to IPv6
    * cname: hostname to another hostname, www.example.com can create, but not example.com
    * NS: name servers of hosted zone
* hosted zones: 
    * public hosted zones: internet accessible
    * private hosted zones: internal records within the VPC
    * pay 0.5$ per month per hosted zone
* this is pretty handy for internal aws resources e.g. load balancer
* manually clickops the ttl (augh) in route53 - can help speed up changes

[cname vs alias](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528190#lecture-article)
* alias records are specific to route53 - works for both root and non-root domains
    * native health check, also free of charge
    * can target: ELB, cloudfront, api gateway, elastic beanstalk, s3 (when buckets as websites), vpc interface endpoints, global accelerator, route53 in same hosted zone
    * CANNOT target EC2 dns names
* cname is non-root only 

[simple routing policy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528192#lecture-article)
* simple routing policy directs traffic to a single resource or multiple IP addresses returned randomly, Alias records with simple routing can only target one AWS resource. if it's any other record, can specify multiple targets

[weighted routing policy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528194#lecture-article)
* directing a percentage of DNS requests to specific resources based on assigned weights, do not need to sum to 100%, aws will compute for you lol
* records must share same name and type to use weighted routing
* use cases include load balancing across regions, testing new application version by controlling traffic distribution... etc 

[latency routing policy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528196#lecture-article)
* redirect users to the resource with the lowest latency, improving user experience, measured by how quickly users connect to the closest AWS region associated with the record

[health checks](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528200#lecture-article) + [hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/28384354#lecture-article)
* health checks are for ec2 instances in hands on - using IPs. common to use `/health` path to correspond status of endpoint (which i had to build into my app, thanks aws lol)
* endpoit health checks: 30 seconds or 10 seconds, http, https or tcp. healthy if >18 of global health checkers report healthy, must allow incoming requests from the Route 53 health checkers' IP address ranges
* calculated health checks: basically combine a bunch of health checks into a single one
* cloudwatch alarm + route53 health check, alarm basically triggers unhealthy

[failover routing policy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528204#lecture-article)
* A record for EC2 instance 1, set routing policy to failover and select primary
* add secondary record with the same name pointing to a different EC2 instance, set failover record type to secondary

[geolocation routing policy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528206#lecture-article)
* Routing policies based on geolocation direct users to specific IPs depending on their physical location.
* A **default record** is essential to handle users from locations not explicitly specified.
* Geolocation routing is useful for website localization, content restriction, and load balancing.
* Health checks and security group settings are critical to ensure proper routing and accessibility.
* hands on uses VPN to test

[geoproximity routing policy](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099214#lecture-article)
* routing traffic based on the geographic location of users and resources; AWS resources use region specifications, while non-AWS resources require latitude and longitude coordinates
* bias value: expand positive, shrink negative
* need to use **route 53 traffic flow** feature