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

### Using AMIs in a different Region
There are different things to take into account when treating with Amazon Machine Images (AMI)
#### Encription and Copying
 | Scenario | Supported |
 |----------|-----------|
 |Unencrypted AMI to unencrypted AMI|Yes|
 |Encrypted AMI to encrypted AMI|Yes|
 |Unencrypted AMI to encrypted AM|Yes|
 |Encrypted AMI to unencrypted AMI|**No**|

 if no encryption specification is given, then it will inherit the encryption of the original one

 If you want to use an AMI in a different region, you have to create a copy and select the desired region, you can choose the encryption but you cannot remove it

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

# S3 
its the service for:
 - Object Storage
 - Scalable
 - Unlimited storage
 - Simple use and low cost
 - Manage data as objects rather than data blocks
 - Max of 5TB objects

All AWS accounts share the S3 namespace, so the name must be globally unique. Here is the format of S3 URL:\
https://**bucket-name**.s3.**Region**.amazonaws.com/**key-name**

After an Upload you'll always see a 200 success code using CLI or API

S3 Characteristics are:
 - Versioning
 - Lifecycle Management, how you want your data to be kept in time
 - Tiered Storage, different tiers to store your data in different scenarios

### Objects
each objects is made of:
 - Key: the name/path of the file in the bucket
 - Value: the object itself
 - Version ID: for storing different versions of the same object
 - Metadata: data about the data e.g. content-type, last-modified, etc.

### Data Security
 - Server-Side Encryption: to encrypt objects when they are stored in the bucket
 - Access Control Lists (ACL): Define which AWS accounts or groups are granted access to the files
 - Bucket Policies: specify what Actions are allowed or denied

## Tiers of Storage
### S3 Standard
 - High Availability and Durability
 - Designed for frequest access
 - Suitable for most workloads
 - e.g. websites, content distribution, mobile/gaming apps, BigData analytics
 - Highest Cost

### S3 Standard-Infrequent Access (S3-IA)
 - when you need to access it couple times per month
 - Rapid Access
 - **You pay to access the data**
 - e.g. long-term storage, backups, disaster recovery files
 - Minimum storage duration: **30 days**
#### S3 ONE ZONE Infrequent Access
 - Costs 20% less than regular S3-IA
 - For long-lived, **non-critical data**
 - The data is ONLY available in ONE Availability Zone
 - same as S3-IA in all other things
### Glacier
 - Long-term data archiving
 - Retrieval times between 1 minute to 12 hours
 - Minimum storage duration: **90 days**
 - **You pay to access the data**
 - e.g. historical data only accessed a few times per year
### Glacier Deep Archive
 - Archives that are rarely accessed
 - Default extraction time of **12 hours**
 - Minimum storage duration: 180 days
 - **You pay to access the data**
 - e.g. financial records that are accessed one or twice per year
### S3 Intelligent Tiering
It is cost-optimized, it changes depending on your access, however there is a minimum storage duration of **30 days**, it offers two tiers:
 - frequent access
 - infrequent access

## Securing S3 Buckets
When a bucket is created, it is by default private, only owned by you, and only you have access to do actions in it

### Bucket Policies
They are declared in a JSON with Actions, actions that are allowed, and Resources, the desired bucket(s) to apply the policy
 - Applied at Bucket Level
 - Not individual Objects
 - Groups of Files
 - Access Control Lists
 - Grant access to objects
 - Fine Grained Control
#### S3 Access Logs
By default is not activate but it can be enable it which will create a log of the different accesses and modifications in the bucket

### S3 Bucket Policy Demo
 - Go to create a bucket, give it a globally unique name
 - Select the bucket, then Upload, select a file from local machine
 - You can access the file by either _Open_ or going through _S3 Object URL_
 - Go to Block public access and disable protection
 - In Edit Bucket Policy, go to create policy, change for principal * for everything and Actions S3:getObject and the bucket ARN, then copy and add it to the bucket policy
 - This should show public access to the bucket and objects
 - Go to the _S3 Object URL_ again and anyone can see it

### S3 as a static html server
 - create a bucket, give it a name, deselect the block access policy
 - Upload a index.html and an error.html to the bucket
 - Go to Bucket properties and select WebServer, then static and put your index and error htmls
 - Go to Bucket policies and create a policy where anyone can perform the action S3:GetObject and save it
 - Go to the link from the created dns and see the index.html from the web browser

## S3 Encryption
it is important to encrypt data in order to protect the assets that are inside of it if the company policies demands it

### Encryption Options
 - Encrypt in Transit
   - SSL/TLS
   - HTTPS
 - Encryption at Rest: Server-Side Encryption
   - SSE-S3: S3 managed Keys (enabled by default)
   - SS3-KMS: AWS KMS managed Keys
   - SS3-C: Customer provided Keys
 - Encryption at Rest: Client-Side Encryption
   - You Encrypt the files before uploading them to S3

### S3 Encryption Configuration Demo
 - Create a Bucket, give it a name and then select the SSE-KMS option
 - Create a KMS key, symmetric for encrypt and decrypt, give it a name
 - Select a key admin user permission, then the usage user permission
 - Back to the S3 refresh the KMS and select the key and create the bucket

## S3 CORS
CORS stands for Cross-Origin-Region-Sharing, it allows one bucket to access the files of another bucket

### S3 CORS Configuration Demo
use case scenario is we have 2 buckets, one will host index.html and the other will host loadpage.html; we're going to update the index file to reference the loadingpage file; test what happens; then configure CORS to allow this to work:
 - Create an IndexBucket; give it a name, unblock the access for public, add the GetObject Policy, enable as Website and add the index.html and error.html
 - Create a CorsBucket; give it a name, unblock the access for public, add the GetObject Policy, enable as Website and add the index.html and error.html BUT also upload the loadpage.html
 - Given the CorsBucket URL, add the _/loadpage.html_, check it works and copy the new URL, change the IndexBucket index.html to redirect to the URL of the loadpage
 - Check that it works by going to the website of the first bucket, click in the html that redirects to the other loadpage
 - You'll see that you get an error in developper tools console in the browser because the Bucket has no access to another Bucket
 - Go to the CorsBucket and edit the CORS, add a policy that allows the authorize, GetMethods and Origin the Website URL of the IndexBucket;
 - Recheck the index of the first bucket to be able to see the other file from the other bucket

## CloudFront
A system of distributed servers which deliver webpages and other web contents. \
It replicates your data in multiple servers around the world to reduce the latency when differents clients around the world want to use your data:
 - Optimized to work with other AWS Web Services
 - Integrated to S3, EC2, ELB and Route53
 - Your Own Server

### Edge Location
is a group of servers in another location so when the client wants to get the data, the request goes first to the EdgeLocation, then to the CloudFront Origin of the data, then it caches the data in that EdgeLocation so when the second client wants the data, it will get it from the cache

### CloudFront Distribution 
Is the name given to the Edge Location with its configuration of the content we want to distribute

### TTL
Its the Time-To-Live of the cached object in the EdgeLocation, normally its configured for 1 day, which will delete the file after 1 day that is was cached.\
If you change files in the Origin Bucket too often, it might be good to change the TTL to the same frequency as the change of the files

### CloudFront & S3 Transfer Acceleration
Enables fast, easy and secure transfers of files over long distances between your end users and an S3 Bucket

### CloudFront Demo
 - Create a Bucket, give it a name and select another AWS Region (the further the better), allow public access
 - Select the Bucket and add policies for public GetObject
 - Upload a big file like a >10mb image
 - Use the URL of the object and open it, see how slow it is
 - Go to CloudFront, create a distribution, select the Redirect the HTTP to HTTPs, select defaults for the rest
 - You can Edit the geography location settings to allow/block certain countries
 - Select the CloudFront URL and use the same Key for the object you want
 - First time it will load slow because its caching for first time, then in another browser try the same URL and it will be way faster

### CloudFront Origin-Access-Identity Configuration DEMO
The idea of this demo is to remove the public read access from our bucket and then allow access only from the CloudFront URL:
 - Create a Bucket, uncheck Block all public access, under Object Ownership choose ACLs enabled
 - Choose a file to upload but just before, in upload permissions, select in Predefined ACLs the Grant public-read access option
 - Create a CloudFront Distribution, select Origin Access control settings, click Create Control Setting
 - Click Copy policy, then go to the Bucket and under Permissions in Policy section, click Edit, then paste in the bucket policy, save changes
 - In the browser check access by going to the CloudFront URL/key of the object
 - Go back to the bucket and in permissions change the access to block all public access
 - This way we can still access the file with the CloudFront URL but not with the S3 Object URL

### CloudFront Allowed Methods
 - GET, HEAD
 - GET, HEAD, OPTIONS
 - GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE
   - Consider restrincting access at your origin level to avoid users deleting certain objects from your origin

use cases:
|HTTP METHOD|DEFINITION|EXAMPLE|
|-----|-----|-----|
|GET|Read data|Read a web page|
|HEAD|Inspect resource headers|Read a web page's header|
|PUT|Send data to create a new resource or replace an existing one|Update data or change the status of a resource|
|PATCH|Partially modify a resource|Modify the contents of a shopping cart|
|POST|Insert data, user to create or update a resource|Comment on a blog post|
|DELETE|Delete data|Remove your email address from a mailing list|
|OPTIONS|Used to find out what other HTTP methods are supported by the given URL|Receive a list of supported HTTP methods|

## Athena
Allows to query the files in your S3 files using federated requests transforming the SQL queries into a S3 extract instruction
 - Serverless: pay per query / per TB scanned
 - Easy: no need to setup, no ETL
 - Integrated: works directly on S3 data

So when would it be useful?
 - Query log files in stored S3
 - Analyze AWS Cost and Usage reports
 - Generate business reports
 - Run queries on click stream data on S3

### Athena Demo
in this use case we will configure a Trail in CloudTrail to generate an audit log of all activity in our account; the logs will be sent to a S3; Use Athena and SQL to query the data stored in the S3:
 - Go to CloudTrail and create one named _management-events_, check that managements events is checked and leave all by defaults
 - The CloudTrail folder will start to populate after 5min, refresh and you'll see the logs
 - Create another S3 Bucket to have a staging space to store the Athena results from the queries
 - Go to Athena, query editor, edit settings and select the staging Bucket
 - In the editor, run the query `CREATE DATABASE athenadb`
 - Run the **Create Table Query** and then the **Query** from [here](https://github.com/ACloudGuru-Resources/course-aws-certified-developer-associate/tree/main/Athena_Demo), Remember to replace the S3 Bucket name with yours in the Location
 
# Serverless Computing

### Serverless 101
many AWS services are serverless which means that it will automatically manage the Capacity Provisioning, Patching, Auto Scaling and High Availability.\
This way the developer only needs to worry about writing the code he needs.

It can give Competitive Advantage since it makes your process faster so Speed to Market, Super Scalable in case you need to make it stringer, Lower Costs since you're never over provisioned, Focus on Applications so that you spend the most time in building your application

List of the Main Serverless Services:
 - Lambda
 - SQS
 - SNS
 - API Gateway
 - DynamoDB
 - S3

## Lambda
Allows to run code without provisioning any servers, it takes care of including the runtime environment, it can be used by different codes of different languages.

Here are some of the features
 - Requests: First million requests per month are free, then $0.20 per million requests
 - Duration: charged by 1ms increments, also the memory allocated to the lambda
 - Price per GB-second: $0.00001667 per GB/s, the first 400,000 GB/s per month are free

### Event-Driven Architecture
Lambda functions can be triggered by other Services, Events or User Requests through HTTP and API Gateway.

Some of the Lambda Triggers are DynamoDB, Kinesis, SQS, Application Load Balancer, API Gateway, Alexa, CloudFront, S3, SNS, SES, CloudFormation, CloudWatch, CodeCommit, CodePipeline, Step Function

## API Gateway
Its the middle man between the client and the backend. An analogy is a restaurant with clients and the chefs, the server will be the API which will communicate the information between both parties, the order will be an HTTP request and the cooked food would be a JSON with information.

API Gateway characteristics are:
 - Publish, Mantain and Monitor APIs
 - A Front Door, to allow a controlled access of data to another services
 - Supported API Types
   - RESTful APIs are optimized for staleless, serveless workloads
   - Websocket APIs are for real-time, two-way, stateful communication e.g. chat apps
 - Supports JSON
 - Directs the traffic to the correct Lambda or Service for every request
 - Supports multiple Endpoints and Targers
 - Supports multiple Versions
 - Integrates with CloudWatch for logging API Calls, latencies and error rates
 - Throttling management so that backend is protected from traffic spikes and denial of service attacks
 
### API Gateway Building a Serverless Website Demo
the use case if to create a python lambda which will be connected to an API Gateway, then we will make an HTTP request which will Trigger the Lambda through the API Gateway
 - Get all the resource materials from [here](https://github.com/ACloudGuru-Resources/course-aws-certified-developer-associate/tree/main/Serverless_Webite_Demo)
 - Create a Lambda Function from scratch and leave all default
 - Put the python code to the Function and Deploy it
 - Add a Trigger, select API Gateway, select Create New with Open Security and check CORS and click Add
 - Open the API Gateway URL and you'll see the response of the lambda in your browser
 - Create a Bucket with ACLs enabled and Disable the Block all public access
 - In Bucket properties go to Edit static website hosting and fill the info with index.html and error.html
 - In the _index.html_ change the `xhttp.open("GET","____", true)` to the new API Gateway URL created, and Upload the two files _index.html_ and _error.html_ to the Bucket
 - Go to the Bucket static website URL and you'll see the button which after the click will change the message in the page
 
