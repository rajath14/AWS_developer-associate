# EC2: Virtual Machines

- [EC2 User Data](#ec2-user-data)
- [EC2 Meta Data](#ec2-meta-data)
- [EC2 Instance Launch Types](#ec2-instance-launch-types)
- [EC2 Pricing](#ec2-pricing)
- [AMIs](#AMIs)
- [EC2 Instances Overview](#ec2-instances-overview)

By default, your EC2 machine comes with:
* A private IP for the internal AWS Network
* A public IP for the WWW

When you SSH into your EC2 machine:
* We can’t use a private IP, because we are not in the same network
* We can only use the public IP

If your machine is stopped and then restarted, the public IP will change

## EC2 User Data
* It is possible to bootstrap our instances using an EC2 User data script
* Bootstrapping means launching commands when a machine starts
* That script is only run once at the instance first start
* Purpose: Ec2 data is used to automated boot tasks such as:
    * Installing updates
    * Installing software
    * Downloading common files from the internet
* The EC2 User Data Script runs with the root user
  
## EC2 Meta Data
* Information about your EC2 instance
* It allows EC2 isntances to "learn" about themselves without having to use an IAM role for that purpose
* Powerful but one of the least known features to developers
* You can retrieve IAM roles from the metadata but **not** IAM policies
* URL: {ec2-ip-address}/latest/meta-data

## EC2 Instance Launch Types 
- **On Demand Instances**: short workload, predictable pricing
- **Reserved Instances**: long workloads (>= 1 year)
- **Convertible Reserved Instances**: long workloads with flexible instances
- **Scheduled Reserved Instances**: launch within time window you reserve
- **Spot Instances**: short workloads, for cheap, can lose instances
- **Dedicated Instances**: no other customers will share your hardware
- **Dedicated Hosts**: book an entire physical server, control instance placement

#### On Demand Instance:
* Pay for what you use
* Has the highest cost but no upfront payment
* No long term commitment
* Recommended for short-term and un-interrupted workloads, where you can’t predict how the application will behave
#### Reserved Instances
* Up to 75% compared to On-demand
* Pay upfront for what you use with long term commitment
* Reservation period can be 1 or 3 years
* Reserve a specific instance type
* Recommended for steady state usage applications (think database)
#### Convertible Reserved Instances
* Can change the EC2 instance type
* Up to 54% discount
#### Scheduled Reserved Instances
* Launch within time window you reserve
* When you require a fraction of a day / week / month
#### Spot Instances
* Can get a discount of up to 90% compared to On-demand
* You bid a price and get the instance as long as its under the price
* Price varies based on offer and demand
* Spot instances are reclaimed within a 2 minute notification warning when the spot price goes above your bid
* Used for batch jobs, Big Data analysis, or workloads that are resilient to failures
* Not great for critical jobs or databases
#### Dedicated Instances
* Instances running on hardware that’s dedicated to you
* May share hardware with other instances in same account
* No control over instance placement (can move hardware after stop / start)
#### Dedicated Hosts
* Physical dedicated Ec2 server for your use
* Full control of Ec2 Instance placement
* Visibility into the underlying sockets / physical cores of the hardware
* Allocated for your account for a 3 year period reservation
* More expensive
* Useful for software that have a complicated licensing model (Bring your own License)
* Or for a companies that have strong regulatory or compliance needs

#### Which host is right for me?
- On demand: coming and staying in resort whenever we like, we pay the full price 
- Reserved: like planning ahead and if we plan to stay for a long time, we may get a good discount. 
- Spot instances: the hotel allows people to bid for the empty rooms and the highest bidder keeps the rooms.You can get kicked out at any time 
- Dedicated Hosts: We book an entire building of the resort

## EC2 Pricing
- EC2 instances prices (per hour) varies based on these parameters:
  - Region you’re in
  - Instance Type you’re using
  - On-Demand vs Spot vs Reserved vs Dedicated Host
  - Linux vs Windows vs Private OS (RHEL, SLES, Windows SQL)
  - You are billed by the second, with a minimum of 60 seconds. 
  - You also pay for other factors such as storage, data transfer, fixed IP public addresses, load balancing
  - You do not pay for the instance if the instance is stopped 

- Example
  - t2.small in US-EAST-1 (VIRGINIA), cost $0.023 per Hour 
  - If used for:
    - 6 seconds, it costs $0.023/60 = $0.000383 (minimum of 60 seconds)
    - 60 seconds, it costs $0.023/60 = $0.000383 (minimum of 60 seconds)
    - 30 minutes, it costs $0.023/2 = $0.0115
    - 1 month, it costs $0.023 * 24 * 30 = $16.56 (assuming a month is 30 days)
    - X seconds (X > 60), it costs $0.023 * X / 3600 
  - The best way to know the pricing is to consult the pricing page: https://aws.amazon.com/ec2/pricing/on-demand/

## AMIs
### What's AMI?
- As we saw, AWS comes with base images such as:
  - Ubuntu
  - Fedora
  - RedHat
  - Windows
  - Etc...
- These images can be customized at runtime using EC2 User data
- But what if we could create our own image, ready to go?
- That’s an AMI – an image to use to create our instances
- AMIs can be built for Linux or Windows machines 

### Why you use a custom AMI?
- Using a custom built AMI can provide the following advantages:
  - Pre-installed packages needed
  - Faster boot time (no need for long ec2 user data at boot time
  - Machine comes configured with monitoring / enterprise software
  - Security concerns – control over the machines in the network
  - Control of maintenance and updates of AMIs over time
  - Active Directory Integration out of the box
  - Installing your app ahead of time (for faster deploys when auto-scaling)
  - Using someone else’s AMI that is optimized for running an app, DB, etc...
- **AMI are built for a specific AWS region (!)**

## EC2 Instances Overview
- Instances have 5 distinct characteristics advertised on the website:
  - The RAM(type,amount,generation)
  - The CPU(type,make,frequency,generation,numberofcores)
  - The I/O (disk performance, EBS optimisations)
  - The Network (network bandwidth, network latency
  - The Graphical Processing Unit (GPU) 
- It may be daunting to choose the right instance type (there are over 50 of them) - https://aws.amazon.com/ec2/instance-types/ 
- https://ec2instances.info/ can help with summarizing the types of instances 
- R/C/P/G/H/X/I/F/Z/CR are specialised in RAM, CPU, I/O, Network, GPU 
- M instance types are balanced 
- T2/T3 instance types are “burstable”
Burstable Instances (T2)
- AWS has the concept of burstable instances (T2 machines) 
- Burst means that overall, the instance has OK CPU performance. 
- When the machine needs to process something unexpected (a spike in load for example), it can burst, and CPU can be VERY good. 
- If the machine bursts, it utilizes “burst credits” 
- If all the credits are gone, the CPU becomes BAD 
- If the machine stops bursting, credits are accumulated over time
- Burstable instances can be amazing to handle unexpected traffic and getting the insurance that it will be handled correctly 
- If your instance consistently runs low on credit, you need to move to a different kind of non-burstable instance (all the ones described before). 

### T2 Unlimited 
- Nov 2017: It is possible to have an “unlimited burst credit balance
- You pay extra money if you go over your credit balance, but you don’t lose in performance
- Overall, it is a new offering, so be careful, costs could go high if you’re not monitoring the health of your instances 




# ELB: Elastic Load Balancers

Load balancers are servers that forward internet traffic to multiple servers (EC2 Instances) downstream

#### Why use a load balancer?
* Spread load across multiple downstream instances
* Expose a single point of access (DNS) to your application
* Seamlessly handle failures of downstream instances
* Do regular health checks to your instances
* Provide SSL termination (HTTPS) for your websites
* Enforce stickiness with cookies
* High availability across zones
* Separate public traffic from private traffic

#### AN ELB (EC2 Load Balancer) is a managed load balancer
* AWS guarantees that it will be working
* AWS takes care of upgrades, maintenance, high availability
* AWS provides only a few configuration knobs

It costs less to setup your own load balancer but it will be a lot more effort on your end. It is integrated with many AWS offerings / services

#### Types of load balancers on AWS
* Classic Load Balancer (v1 - older generation - 2009)
* Application Load Balancer (v2 - new generation - 2016)
* Network Load Balancer (v2 - new generation - 2017)
* You can setup internal or external ELBs

#### Health Checks
* Health checks are crucial for load balancers
* They enable the load balancer to know if instances it forwards traffic to are available to reply to requests
* The health check is done on a port and a route (/health is common)
* If the response is not 200 (OK), then the instance is unhealthy

#### Application Load Balancer (v2)
* Application load balancers (Layer 7) allow to do:
  * Load balancing to multiple HTTP applications across machines (target groups)
  * Load balancing to multiple applications on the same machine (ex: containers)
  * Load balancing based on route in URL
  * Load balancing based on hostname in URL 
* Basically, they’re awesome for micro services & container-based application (example: Docker & Amazon ECS) 
* Has a port mapping feature to redirect to a dynamic port 
* In comparison, we would need to create one Classic Load Balancer per application before.That was very expensive and inefficient!
* Good to Know
    * Stickiness can be enabled at the target group level
        * Same request goes to the same instance
        * Stickiness is directly generated by the ALB (NOT the application)
    * ALB supports HTTP/HTTPS & Web sockets protocols
    * The application servers don’t see the IP of the client directly
        * The true IP of the client is inserted in the header X-Forwarded-For
        * We can also get Port (X-Forwarded-Port) and protocol (X-Forwarded-Proto)
#### Network Load Balancer (v2)
* Layer 4 allow you to do:
    * Forward TCP traffic to your instances
    * Handle millions of requests per second
    * Support for static IP or elastic IP
    * Less latency ~100ms (vs 400 ms for ALB)
* Network Load Balancers are mostly used for extreme performance and should not be the default load balancer you choose
* Overall, the creation process is the same as the Application Load Balancer

#### Load Balancers Good to Know
* Any Load Balancer (CLB, ALB, NLB) has a static host name. They do not resolve and use underlying IP
* LBs can scale but not instantaneously - contact AWS for a “warm up”
* NLB directly see the client IP
* 4xx errors are client induced errors
* 5xx errors are application induced errors
    * Load balancer Errors 503 means at capacity or no registered target
* If the LB can’t connect to your application, check your security




# Security Groups

#### The fundamental of network security in AWS

* Can be attached to multiple instances
* Locked down to a region / VPC combination
* Does live “outside” the EC2 - if traffic is blocked, the EC2 instance won’t see it
* It’s good to maintain one separate security group for SSH access
* If your application is not accessible (time out), then it’s usually a security group issue
* If your application gives a “connection refused” error, then it’s an application error or its not launched
* All inbound traffic is blocked by default
* All outbound traffic authorized by default

#### Security groups act as a firewall on EC2 Instances
They regulate:
* Access to ports
* Authorized IP ranges - IPv4 and IPv6
* Control of inbound network
* Control of outbound network




# EBS Volume

* An EC2 machine loses its root volume (main drive) when it is manually terminated.
* Unexpected terminations might happen from time to time (AWS would email you)
* Sometimes, you need a way to store your instance data somewhere
* An EBS (Elastic Block Store) Volume is a network drive you can attach to your instances while they run
* It allows your instances to persist data

#### EBS Volume
* It’s a network drive (Not a physical drive)
    * It uses the network to communicate the instance, which means there might be a bit of latency
    * It can be detached from an EC2 instance and attached to another one quickly
* It’s locked to an Availability Zone (AZ)
    * An EBS Volume in us-east-1a cannot be attached to us-east-1b
    * To move a volume across, you first need to snapshot it
* Have a provisioned capacity (size in GBs and IOPs)
    * You get billed for all the provisioned capacity
    * You can increase the capacity of the drive over time

#### EBS Volume Types
- EBS Volumes come in 4 types 
- GP2 (SSD): General purpose SSD volume that balances price and performance for a wide variety of workloads 
- IO1 (SSD): Highest-performance SSD volume for mission-critical low-latency or high- throughput workloads 
- ST1 (HDD): Low cost HDD volume designed for frequently accessed, throughput- intensive workloads 
- SC1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads 
- EBS Volumes are characterized in Size | Throughput | IOPS
- When in doubt always consult the AWS documentation
-  Only GP2 and IO1 can be used as boot volumes

#### EBS Volume Types Use Cases
1. GP2
- Recommended for most workloads 
- System boot volumes
- Virtual desktops
- Low-latency interactive apps
- Development and test environments

2. IO1
- Critical business applications that require sustained IOPS performance, or more than 16,000 IOPS per volume (gp2 limit)
-  Large database workloads, such as: MongoDB, Cassandra, Microsoft SQL Server, MySQL, PostgreSQL, Oracle

3. ST1
- Streaming workloads requiring consistent, fast throughput at a low price. 
- Big data, Data warehouses, Log processing
- Apache Kafka
 - Cannot be a boot volume
 
4. SC1
- Throughput-oriented storage for large volumes of data that is infrequently accessed
- Scenarios where the lowest storage cost is important
- Cannot be a boot volume

#### EBS Volume Types Summary
- gp2: General Purpose Volumes (cheap)
- io1: Provisioned IOPS (expensive)
- st1: Throughput Optimized HDD
- sc1: Cold HDD, Infrequently accessed data

#### EBS Volume Resizing
* Feb 2017: You can resize your EBS Volumes
* After resizing an EBS volume, you need to repartition your drive

EBS Snapshots
* EBS Volumes can be backed up using “snapshots”
* Snapshots only take the actual space of the blocks on the volume
* If you snapshot a 100GB drive that only has 5 gb of data, then your EBS snapshot will only be 5 gb
* Snapshots are used for:
    * Backups: ensuring you can save your data in case of catastrophe
    * Volume migration
        * Resizing a volume down
        * Changing the volume type
        * Encrypt a volume

#### EBS Encryption
* When you create an encrypted EBS volume, you get the following:
    * Data at rest is encrypted inside the volume
    * All the data in flight moving between the instance and the volume is encrypted
    * All snapshots are encrypted
    * All volumes created from the snapshots are encrypted
* Encryption and decryption are handled transparently (you have nothing to do)
* Encryption has a minimal impact on latency
* EBS Encryption leverages keys from KMS (AES-256)
* Copying an unencrypted snapshot allows encryption

#### EBS vs. Instance Store
* Some instance do not come with Root EBS volumes
* Instead, they come with “instance Store”
* Instance store is physically attached to the machine
* Pros:
    * Better I/O performance
* Cons:
    * On termination, the instance store is lost
    * You can’t resize the instance store
    * Backups must be operated by the user
* Overall, EBS-backed instances should fit most applications workloads

#### EBS Summary

* EBS can be attached to only one instance at a time
* EBS are locked at the AZ level
* Migrating an EBS volume across AZ means first backing it up (snapshot), then recreating it in the other AZ
* EBS backups use IO and you shouldn’t run them while your application is handling a lot of traffic
* Root EBS Volumes of instances get terminated by default if the EC2 instance gets terminated. (You can disable that)
* In some cases, it's better to externalize your RDS database so that it won't get deleted when you delete your elastic beanstalk enviornment
* Elastic Beanstalk relies on CloudFormation

#### EBS Volume Types - Use cases 

* Big Data / Data Warehouses / Log Processing : ST1 (HDD)
* Lowest storage cost : SC1 (HDD)
* NoSQL such as MongoDB, Cassandra or MSQL : IO1 (SSD)
* Low latency applications : GP2 (SSD) 




# ASG: Auto Scaling Group

In real-life, the load on your websites and applications can change. You can create and get rid of servers very quickly

The goal of an Auto Scaling Group (ASG) is to:
* Scale out (add EC2 Instances) to match an increased load
* Scale in (remove EC2 Instances) to match a decreased load
* Ensure we have a minimum and a maximum number of machines running
* Automatically register new instances to a load balancer

#### ASGs have the following attributes
* A launch configuration
    * AMI + Instance Type
    * EC2 User Data
    * EBS Volumes
    * Security Groups
    * SSH Key Pair
* Min Size / Max Size / Initial Capacity
* Network + Subnets Information
* Load Balancer Information
* Scaling Policies

#### Auto Scaling Alarms
* It is possible to scale an ASG based on CloudWatch alarms
* An alarm monitors a metric (such as Average CPU)
* Metrics are computed for the overall ASG instances
* Based on the alarm:
    * We can create a scale-out policies (increase the number of instances)
    * We can create a scale-in policies (decrease the number of instances)

#### New Auto Scaling Rules
* It is now possible to define “better” auto scaling rules that are directly managed by EC2
    * Target Average CPU Usage
    * Number of requests on the ELB per instance
    * Average Network In
    * Average Network Out
* These rules are easier to set up and can make more sense

#### Auto Scaling Custom Metric
* We can auto scale based on a custom metric (ex: number of connected users)
* 1. Send custom metrics from an application on EC2 to CloudWatch (PutMetric API)
* 2. Create a CloudWatch alarm to react to low / high values
* 3. Use the CloudWatch Alarm as the scaling policy for ASG

#### ASG Summary
* Scaling policies can be on CPU, Network… and can even be on custom metrics or based on a schedule (if you know your visitors patterns)
* ASGs use Launch configurations and you update an ASG by providing a new launch configuration
* IAM roles attached to an ASG will get assigned to EC2 instances
* ASG are free. You pay for the underlying resources being launched
* Having instances under an ASG means that if they get terminated for whatever reason, the ASG will restart them. Extra safety
* ASG can terminate instances marked as unhealthy by an LB (and hence replace them)