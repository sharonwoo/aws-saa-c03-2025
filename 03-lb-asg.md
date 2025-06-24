# elastic load balancers

[about scalability](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13531374#lecture-article)
* Vertical scaling: Increasing instance size, such as from t2.nano (0.5 GB RAM, 1 vCPU) to a1.12tb1.metal (12.3 TB RAM, 450 vCPUs).
* Horizontal scaling: Increasing the number of instances, known as scaling out (adding instances) or scaling in (removing instances).
* High availability: Running the same application instance across multiple availability zones, enabled in auto-scaling groups or load balancers.

[elastic load balancer overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528090#lecture-article)
* **A load balancer is a server or a set of servers that forward received traffic to multiple backend or downstream EC2 instances or servers**; Users do not know which backend instances they are connected to; they only connect to the Elastic Load Balancer, which provides a single endpoint for connectivity.
* Bennies: Provides a single point of access to your applications. Seamlessly handles failures of downstream instances through health check mechanisms. Supports health checks to determine instance availability. Provides SSL termination for HTTPS encrypted traffic. Enforces stickiness with cookies.
Ensures high availability across zones. Separates public traffic from private traffic in your cloud environment.
* Types of load balancers: 
    * Classic Load Balancer (CLB): Older generation from 2009, supports HTTP, HTTPS, TCP, SSL, and Secure TCP. It is deprecated and shown as such in the AWS console but still available.
    * Application Load Balancer (ALB): Introduced around 2016, supports HTTP, HTTPS, and WebSocket protocols.
    * Network Load Balancer (NLB): Introduced in 2017, supports TCP, TLS, Secure TCP, and UDP protocols.
    * Gateway Load Balancer (GWLB): Introduced in 2020, operates at the network layer and supports the IP protocol.
* recall that the security group of the node should only allow traffic from the load balancer (which can reference the security group of the load balancer), while the load balancer typically allows inbound on 80/443 from anywhere (`0.0.0.0/0`)

[application load balancer](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078003#overview)
* http, layer 7
* load balancing across machines (defined in target groups)
* path based routing (since layer 7....) - hostname, parameter/query string based etc... 
* target groups can be either ec2 instances, ecs tasks, lambda functions, private ips (used for on prem)
* **fixed hostname** with ALB e.g. `alb.staging.domain.com`
* The application servers do not see the client's IP address directly. Instead, the true client IP is inserted into the HTTP header called X-Forwarded-For.
* You can also obtain the client port using X-Forwarded-Port and the protocol used via X-Forwarded-Proto.
* **note if you make one for eks you NEED the health check configured else it will spam you with errors**

```
# also see deployment and service, obviously

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mvp-ingress
  namespace: some-namepsace
  labels:
    app.kubernetes.io/name: mvp-staging
  annotations:
    kubernetes.io/ingressClassName: alb
    alb.ingress.kubernetes.io/scheme: internal
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    alb.ingress.kubernetes.io/ssl-policy: "ELBSecurityPolicy-2016-08"
    alb.ingress.kubernetes.io/certificate-arn: some arn
    alb.ingress.kubernetes.io/load-balancer-name: mvp-alb
    alb.ingress.kubernetes.io/security-groups: meaningful-security-group-name-because-the-original-was-not
    alb.ingress.kubernetes.io/subnets: subnet-01,subnet-08
    alb.ingress.kubernetes.io/group.name: mvp
    alb.ingress.kubernetes.io/healthcheck-path: /health
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '300'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'
spec:
  ingressClassName: alb
  rules:
  - host: staging-load-balancer.domain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mvp-service
            port:
              number: 443
```

[alb hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/35136194#lecture-article) and [part 2](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26099120#lecture-article)
* use cases: 
    * The Application Load Balancer is designed for HTTP and HTTPS traffic.
    * The Network Load Balancer operates on TCP and UDP protocols or TLS over TCP. It is used when ultra-high performance is required, such as millions of requests per second with ultra-low latency.
    * The Gateway Load Balancer is used for security purposes, including intrusion detection and firewalls, analyzing network traffic.
* remember it takes a while to provision

[network load balancer](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078013#lecture-article)
* tcp and udp traffic directly, points to IPs. the actual rule is p similar to ALB (type: HTTP, protocol: TCP, port etc, source the sg - for example)
* can put an NLB in front of an ALB (which we saw at work)

[gateway load balancer](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/28874688#lecture-article)
* layer 3 - handles IP packets!

[sticky sessions for elb](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528094#lecture-article)
* client making multiple requests to the load balancer to be routed to the same backend instance consistently
* ALB, NLB (and classic but that's deprecated)
* COOKIE - application based (custom generated by application) or duration

[cross zone load balancing](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078093#lecture-article)
* cross zone load balancing, each load balancer instance distributes traffic evenly across all registered instances in all availability zones - so basically ignores the region the load balancer is in, it can direct to the other region's instances
* enabled by default
* Normally, AWS charges for data transferred between availability zones, but with the Application Load Balancer's default setting, these charges do not apply.
* For the Network Load Balancer and Gateway Load Balancer, cross zone load balancing is disabled by default. Enabling it will incur charges for data transferred between availability zones.

[ssl/tls certs](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078099#lecture-article)
* An SSL certificate enables encrypted traffic between clients and load balancers during transit. This encryption is known as in-flight encryption, meaning data traveling through a network is encrypted and can only be decrypted by the sender and the receiver. TLS is the new one...
* From a load balancer perspective, users connect over HTTPS, which uses SSL certificates to encrypt the connection. This secure connection occurs over the public internet to the load balancer. Internally, the load balancer performs SSL certificate termination, decrypting the traffic. It then communicates with backend EC2 instances using HTTP, which is unencrypted but occurs over a private Virtual Private Cloud (VPC) network, providing some security.
* The load balancer loads an X.509 certificate, also known as an SSL or TLS server certificate. AWS Certificate Manager (ACM) is used to manage these certificates in AWS. You can also upload your own certificates to ACM if desired. When setting up an HTTPS listener on a load balancer, you must specify a default certificate. Additionally, you can add an optional list of certificates to support multiple domains. Clients use Server Name Indication (SNI) to specify the hostname they are reaching, allowing the load balancer to select the appropriate certificate.