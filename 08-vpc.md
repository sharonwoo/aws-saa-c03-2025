# vpc

[introduction](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528534#overview) - covers all of this stuff

![VPC componentis diagram](vpc.png)

[cidr vs public ip](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/13528536#lecture-article) - Classless Inter-Domain Routing
* **base ip** e.g. `10.0.0.0`
* **subnet mask** - how many bits can change in the ip address. e.g. `/0` (everything), `/32` (only 1 ip address), or it can look like `255.0.0.0` aka `/8`
* between the base ip + subnet mask is **a range of ip addresses*
* common ranges: 
    * `/32`: a single IP address
    * `/24`: last octet can change, e.g. e.g. `192.168.0.0` - `192.168.0.255`
    * `/16`: last 2 can change
    * `/8`: last 3 can change
    * `/0`: EVERYTHING CHANGES AND NOTHING STAYS THE SAME
    * each step down means either halving or doubling. so `/31` is 2, `/30` is 2^2... 
* private IP address ranges defined by IANA: 
    * `10.0.0.0/8`: A large private IP range commonly used in big networks.
    * `172.16.0.0/12`: Another private IP range.
    * `192.168.0.0/16`: Typically used for home networks; devices connected to a home router often have IPs starting with 192.
    * All other IP addresses outside these ranges are public IP addresses used on the internet

