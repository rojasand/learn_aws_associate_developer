# learn_aws_associate_developer
repo for adding interesting codes and documenting the information for the certification

# starting date 22 January 2024
according to the website, for 24h per week of learning, the candidate is considered ready for presenting the exam the **5 of february**

# IAM

## Users
There are:
 - Users: which we can add specific policies
 - Groups: add users to one or multiple groups, each group having a set of permissions
 - Roles: Give a role to a service or person to have a set of permissions depending on their execution

 ## Login
 there exists:
 - MultiFactorAuthentication
 - single URL login for the different users, all they need to do is go to the link and put their login and password
 - It is also possible to set rules to the passwords like complexity, length and expire time

 ## Best Practices
 Always give the least access possible to the different Users/Groups
 It is also recommended to add Users to a Group so that its easier to control many people at one

 # EC2

 ## Pricing
 the pricing of the service EC2 depends on the type of demand:
 - On demand: pay as much as you use
 - Reserved: you make a commitment of certains machines of certain type for a N amount of time (save money++), possibility to upgrade to higher machine
 - Spot: you use machines that are available at the moment and there is not so much demand so the price is less but if the demand is higher, you machine is terminated or freezes
 - Dedicated: the machine is yours but is more expensive, only use if strict restrictions or softwares with one machine license 

## Instances
there many different types of instances depending on the requirements:
 - General purpose: balanced computing and network
 - Compute optimized: deisgned for more CPU power, batch processing, analytics, high performance science, etc
 - Memory optimized: fast performance for high memory computations like large datasets to be loaded
 - Accelerated computing: for DeepLearning or MachineLearning scenarios, also FPGAs (for faster computing in the market vs competitor)
 - Storage optimized: for a big amount of storage and attach harddisks, good for databases

## Creation of an EC2 Instance
In this example we're going to launch a EC2 web server to be available online with HTTP and also SSH for us to connect and manipulate our machine from our local computer:
 - go to EC2 and create and launch and instance
 - give it a name
 - select the VPC
 - select the security group
 - allow SSH connection and create a .pem key, then download it
 - allow the SSH and the HTTP connection with anywhere as the source
 - create the instance
 - with the downloaded ssh.pem key, perform: `chmod 400 key.pem`
 - connect to the EC2 instance by clicking in the instance and the IP address, then perform ssh: `ssh ec2-user@IP_ADDRESS -i key.pem`
 - now for the creation of the HTTP server:
   - `sudo su` for root privilege
   - `yum update -y` to update all packages and system kernel
   - `yum install httpd -y` to install the apache server
   - `systemctl start httpd` to launch the server apache
   - `systemctl enable httpd` to enable the auto launch of the server at startup of the EC2 instance
   - `system status httpd` to check that the server is running
   - `cd /var/www/html` to go to the default apache server folder
   - `nano index.html` put inside whatever you want for the website
   - now copy and paste the public IP address in the browser and you should see the website!

## EC2 Instance Connect
it allow to connect directly as ssh terminal to the machine without the need of downloading or configuring anything in your local machine

## EBS volumes
Elastic Block Store, they can be attached to the EC2 instance. So it is like adding new volumes, so for example Create a file system, manage a database, ect.. It is great to use for the following reasons:
 - Procution Workloads: designed for critical workload
 - Highly available: automatically replicated to a single Available Zone to protect against hardware failure
 - Scalable: you can change it and increase the volume

You use different volumes:
 - General Purpose (gp#) : which are good for average performance (max 16K IOPS)
 - Provisioned IOPS (io#) : more expensive but high performance (max 64K IOPS) and more durability
 - Provisioned IOPS SSD Block Express : with Storage Area Network (SAN) critical high performance (max 256k IOPS)
 - Throughput Optimized HDD (st#) : low cost HDD volumes so when you need to stock/manipulate a huge amount of data like ETL, BigData, etc. Also it cannot be a BOOT volume
 - Cold HDD (sc#) : lowest cost option HDD for colder data when performance is not a requirement

### EBS Encryption:
If your account is set by default to encrypt, then you cannot create an uncrypted volume. Also you can create snapshots which is like a backup of another volume BUT if the origin volume is encrypted then all snapshots will be also encrypted

## ELB Elastic Load Balancer
It distributed the network traffic across a group of servers. So for example if one servers goes down, it will automatically redirect all traffic to the other servers. Here are the different type of Load Balancers:
 - Application Load Balancer : HTTP and HTTPS
   - It is application aware, 7 Layer Model:
     - **Layer 7: Application** 
     - Layer 6: Presentation
     - Layer 5: Session
     - Layer 4: Transport
     - Layer 3: Network
     - Layer 2: Data Link
     - Layer 1: Physical
 - Network Load Balancer : TCP and high performance **Layer 4** so more expensive too
 - Classic Load Balancer : LEGACY HTTP/HTTPS and TCP **Layer 7 & 4**
 - Gateway Load Balancer : load balance for 3rd party virtual appliances running in AWS like things purchased in AWS Marketplace, Virtual firewalls, IDS/IPS systems

### X-Forwarded-For Header
identify the originating IP address of a client connecting throught a load balancer, this way the final Instance will be able to see the first original IP of the client instead of the middle ELB

### Common Load Balancer Errors
 - Error 504 Gateway Timeout: check the application, could not connect to the target, database, lambda

### Hands On Creation ELB
 - Create the Load Balancer
 - Create a Target Group (TG) which is the final service of the traffic
 - Add the Instances you want to the TG and then launch
 - Check that the health is OK for the LB, so normally when clicking in the LB, you should see the TG and in the TG you should see the instance 

## Route53
AWS DNS service which allows to map a domain to a EC2 Instances, Load Balancers and S3 Buckets

### Hands On Route53 to EC2
before: [EC2 Load Balancer Creation](#hands-on-creation-elb)
 - go to Route 53, Hosted Zones
 - Create a DNS Record, so its a mapping between our route 53 domain name and the Application Load Balancer
 - Create an Alias to Application and Classic Load Balancer
 - Choose a region 
 - Select the desired Load Balancer with simple routine for standard DNS only one
 - Create it and check the mapping between the domain name and our Load Balancer, copy paste the domain name on the browser and voila

## AWS CLI
we are going to use the command line interface to manipulate certain services

### Demo
 - Create an EC2 with ssh and pem key, then select the public IP address
 - SSH into the EC2, then run `aws configure`
 - Go to IAM and select a user in order to create access keys to the account
 - Insert the Access ID and Access Key
 - Give to that user the S3AllAccessKey
 - in the CLI write `aws s3 ls` and there should be no error
 - check the `~/.aws/credentials` to see the declared credentials
 - check `aws configure list` to see the existing credentials

### Pagination
normally the Aws CLI is limited for a maximum amount of objects it can return, so pagination allows to split the amount in batches.\
`--page-size` argument allow to decide the size of the batch\
`--max-items` for s3 to limit the amount of object to treat

## CloudShell
its a Virtual Linux Terminal to manage your AWS environment \
The `Actions` button allows to upload/download files.\
`aws s3 mb s3://uniquename123` to create a bucket\
`aws s3 ls` to list the buckets

## Launching a EC2 instance with S3 Role
 - in IAM go to Roles, create an Aws Service Role, select common use EC2
 - go to Permissions Policies, and search _s3_ and select AmazonS3FullAccess
 - go to your instance in EC2 and select the new Role
 - login with EC2 Instance Connect
 - `aws s3 ls` should give no errors
 - `aws s3 mb s3://BucketName` creates a bucket
 - `echo "test" > hello.txt` to create a file
 - `aws s3 cp hello.txt s3://BucketName` to copy the file to the bucket
 - `aws s3 ls s3://BucketName` should list the new file

### Best Practices
ALWAYS avoid hard coding your credentials in the services, it is better to create Roles and change their policies and attach them to whatever you need them

## EC2 Image Builder
automates the process of creating and maintaining your images.\
When software updates are available, Image Builder can automatically create a new image, run validation tests and make it available to the AWS region of your choise.

Allows to share your AMI to other accounts you own

How it works?
 1. Base OS: base OS image e.g. Linux Amazon 2 AMI
 1. Software: define the things to install
 1. Test: run tests to check if all ok
 1. Distribute: to the regions of your choice

Terminology:
 - Image Pipeline: defines the configuration of end-to-end process 
 - Image Recipe: creates a recipe for each image which can be shared and controlled 
 - Build Components: the software components to include in the image
 - e.g. Source Image = Linux, Build Component = Apache Tomcat

### Demo Build an EC2 from an Image Builder
 - Create an IAM Role AWS Service EC2 with policy **EC2InstanceProfileForImageBuilder** and **AmazonSSMManagedInstanceCore**
 - Go to EC2 Image Builder and create an Image Pipeline with manual schedule
 - In configuration create a new Recipe of type Amazon Machine Image (AMI) with version 1.0.0 and keep defaults after, for image name AmazonLinux2x86
 - In Components find all the software you want to update the image with every you need
 - In Test Components find all the tests you need like boot, check if its ok to boot
 - In Infrastructure configuration create a new configuration, add a name and **add the role created before**, declare the machine and go with defaults
 - In Distribution check that everything is ok, then review all
 - Create Pipeline and run it
 - Check in EC2 Instances and you'll see it pop up with the test


# RDS
the service for creating relational data bases with transactions\
there are many DBs that can be created: SQLserver, MySQL, PSQL, AmazonAurora, Oracle, MariaDB; however it is made more optimized and with scalability for MySQL and PSQL servers.

For comparison, a manual installation of a full DB on fresh servers would take 8 days setting everything up and testing to make it into prod.

### (OLTP) Online Transaction Processing 
For processing data from transactions in real-time, e.g. customer orders, banking transaction. It is all about data processing, and completing large numbers of small transactions in real-time\
**RDS IS MADE FOR THIS**

### (OLAP) Online Analytics Processing
Processes complex queries to analyze large amount of data. It is all about data analysis using large amount of data, and complex queries that take a long time to complete\
**RDS IS NOT MADE FOR THIS**

### Launch an RDS Instace
 - Create an RDS with MySQL and free tier, default VPC, new SecurityGroup named rds-sg, admin logins and password authentication
 - there exists backups, cloudlogs, maintenance, final cost per month estimate
 - Create an EC2 instance with a SecurityGroup ec2-sg and do `yum install mysql -y` and `mysql --version` to check if OK
 - Go to VPC Security Groups and select **rds-sg**, inbound rules, add a rule for mysql: MYSQL, with source **ec2-sg** 
 - the new Security Group inbound rule will allow EC2 MySQL Client to connect to the RDS
 - Connect to RDS from the EC2: `mysql -u RDS_USER -p -h YOUR_RDS_ENDPOINT RDS_NAME`
 - `status` will show the status of the given RDS DB

## RDS Multi-AZ and Read Replicas
these two are kind of similar extra services to RDS

### Multi-AZ
When you have a primary RDS in us-east-1a, then it will make a copy to a standby RDS in us-east-1b, so when the AZ is down, we have the other one and all applications will point to it.\
Aws handles all the replication for you and synchronize the production base to the standby one, all types of RDS BD are compatible with this service.\
Mostly for Risk Management and Recovery but not for improved performance

### Read Replicas
Its a read-only copy of the primary database, e.g. and App server writes and read from a primary RDS, but there is a BI Server that only reads data for reporting, so it will read the replica, also the replica can be created in the same AZ, different one or even a different Region, plus each Replica can have its own DNS endpoint.\
Read replicas can be promoted to independant DB for read and write\
So they're good for Scaling but not for Disaster Recovery

## RDS Backup and Snapshots
Any Base that is recovered will create a new RDS with new DNS endpoint

### Database Snapshots
are made manually, ad-hoc and user initiated.\
Not automated, No retention period, able to go back to whichever point you want

### Automated backups
programmed backups with certain times, also a retention period to roll-back to any point you need. It makes a full daily backup, and then in recovery it loads it back and performs the transaction logs to update it to present (retention period is from 1 to 35 days)\
During the backup window the IO of the Base might be slow

### Encryption at Rest
Enable Encryption at creation time by selecting the option in the console, it cannot be changed after\
It uses KMS which is the AWS Key Management Service (AES-256 encryption)\
Any RDS storage and replicas will also be encrypted\
In case it is needed to be encrypted, create a snapshot, encrypt it and then restore it.

## RDS Proxy
Your application will point towards a RDS Proxy, which pools and shares DB connections to assist with application Scalability and Efficiency. Then RDS receives the information from the application through the RDS Proxy

### RDS Proxy Fault Tolerance
It must be seen like a LoadBalancer, e.g. multiple clients using the app will be connected to the RDS Proxy, which it will then decide where to redirect the connection to an available RDS:
 - It is Serverless and scales automatically
 - Preserves application connection 
 - Detects failtover and routes requests
 - Deployable over Multi-AZ for protection
 - Up to 66% faster failover time

User Cases:
 - Apps with unpredictable workloads
 - Apps that open/close database connections infrequently
 - Apps that require availability through transient failures
 - Apps with open but idle connection

# ElastiCache
its faster to extract a value when it is in memory rather than stored:
 - In-Memory Cache (Key Value), easier deployment, operation and scaling
 - Improves DB Performance, information retrieval from fast, in-memory caches instead of disks
 - Great for Read-Heavy Database Workloads, caching the results of I/O intensive database queries, also storing session data for distributed applications

## Types Of ElastiCache

### Memcached
Great for basic object caching, scales horizontally, but there is no persistence, Multi-AZ or failover.\
Good choice for simplicity caching

### Redis
More sophisticated solution with entreprise features like persistence, replication, Multi-AZ and failover.\
Supports sorting and ranking dataand complex data types like lists and hashes.

### Typical Scenario
 - Database Under Stress
 - Find a Solution
 - Know when to use ElastiCache

NOT USEFUL WHEN:
 - Heavy Write Loads
 - OLAP Queries, data warehouse like redshift would be better solution

# MemoryDB for Redis
 - Massively Scalable, in-memory (GB to >100TB)
 - Highly Available, Multi-AZ, transaction log for recovery
 - Primary Database, entire dataset of an application can be stored in memory (instead of a DB + Cache)
 - Ultra-Fast Performance, supports >160million requests per second with microsend readn and milisecond write latency

Suitable uses like:
 - Ultra-fast Workloads
 - Applications that need high performance in-memory DB to handle millions of requests per second
 - Applications with Data intensive, low latency and high scalability requirements
 - Highly Scalable microservices architectures
 - E.g. Online Video Games 

## Difference between ElastiCache for Redis and MemoryDB for Redis
### ElastiCache
 - DB Cache
 - Fast but not ULTRA fast
 - E.g. website that needs to store session data
### MemoryDB
 - Primary DB
 - ULTRA fast
 - E.g. video game exchanging millions of 3D assets 

# Systems Manager: Parameter Store
Centralized storage and management of your secrets and configuration data such as passwords, database strings, and license codes. You can encrypt values, or store as plain text, and secure access at every level.\
It can be integrated with EC2, CloudFormation, Lambda, CodeBuild, CodePipeline and CodeDeploy

# Secrets Manager
Allows to protect and store secrets both inside and outside of you AWS environment, automate a rotation of secrets, Secure with fine-grain permissions and encryption with KMS

Secret Types:
 - RDS DB
 - RedShift Cluster
 - DocumentDB DB
 - Other DB
 - API Keys

Secrets are encrypted using an AWS KMS Key, Customer Master Key (CMK) which is the logical representation of a master key which allows to decrypt data (up to 4KB of data)\
They can also rotate every 30,60,90 or custom # of days, a lambda needs to be created or used for the secret rotation to update the secret

## Difference between Secrets Manager and System Manager
### Secrets Manager
 - Database Credentials
 - API Keys
 - Rotation of Keys
 - **For Confidential information**
### Systems Manager
 - Wider Use Cases
 - Configuration Variables 
 - License Keys
 - **For Configuration informations**
