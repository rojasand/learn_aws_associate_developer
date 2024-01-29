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

It has been improved to be able to import OpenAPI to get a configuration API right away.

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

### API Gateway Mock Integration
you can create mock integration endpoints so that you can simulate the kind of response the Application Backend to allow developpement of the App independently, the mock endpoints are:
 - CREATE
 - TEST
 - DEBUG

### API Gateway Stage
it references the lifecyle state of the API (dev, prod, etc..).\
Each stage can be associated with a different endpoint.\
**Stages Variables** are Key:Value pairs that act like environment variables so that you can change the behaviour of your API depending on them

### API Gateway Request Transformation
it allows to transform the requests as they are passing through the API Gateway in order to add, change or delete certain informations as needed

### API Gateway Caching
Allows to reduce the number of calls made to your endpoints by caching the result from requests, it uses by default a TTL of 300 seconds

### API Gateway Account Level Throttling
For preventing your API from being overwhelmed by too many requests
 - Default limits of 10k requests per second per Region
 - Concurrent requests, 5k requests across all APIs per Region
 - 429 Error when too many requests
 
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

### Lambda Version Control
As soon a Lambda is created it has a versio called `$LATEST` and lets say you need to update the Lambda, so the latest versio will be attached to it.\
Keeping different type of versions is useful for:
 - Multiple versions to keep backups
 - Alias to point to specific version of the function code
   - Important to change Aliases when new versions are updated and needed

#### Lambda Version Control Demo
 - Create a Lambda with latest Nodejs 
 - Upload a first version of a code and deploy
 - Test the Lambda
 - In Actions, select public new version and give it a description, it will be VERSION 1 with a `:1` in the end of the ARN
 - In Actions, select create Alias and give it name `prod` and version select 1
 - Now the Lambda will have **Alias:Prod** and the ARN will have `:prod` in the end of the ARN
 - Now we upload and updated version of the code, Deploy and Test it
 - Check that the ARN is now normal without any suffix because it points to `$LATEST`
 - Now publish a new versino from `$LATEST` and give it another description 
 - The ARN will now have `:2` as suffix
 - Create a new Alias called **Test** with the version 2
 - The Lambda will now have **Alias:Test** and `:Test` as suffix of the ARN
 - e.g. of a Alias ARN: arn:aws:lambda:**Region**:**Account**:function:**LambdaName**:**ALIAS**

### Lambda Concurrent Executions
It allows to limit how many executions of your function can happen in parallel, this is to enable throttling. The default limit is 1009 concurrent executions per second.\
The **error 429 HTTP** is when your Lambda invocations are beign rejected because of the limit executions.\
To raise the limit exexutions, you can contact AWS support.\
**Reserved Concurrency** is a limit that guarantees a set number of concurrent executions are always available to a critical functions

### Lambda And VPC Access Demo
In this Use case, we have a Lambda that needs to access data from a EC2 or RDS that are inside a Security Group, which is inside a Private VPC, so the Lambda must have access to this VPC in order to have access; For this its important to have the **VPC**, **Subnets** and **Security Groups**:
 - Create a VPC with subnets and Security Groups
 - Create a Lambda, by default it only has the right to write in CloudWatch Logs
 - Go to the Lambda Role and add the **AWSLambdaVPCAccessExecutionRole** permission
 - By default there is no VPC in a Lambda, so edit and select the one you created before
 - Now you will have access to the other resources

### Lambda Ephemeral and Persistent Data Storage Patterns
Lambda is stateless so it means that you cant keep any data, everything is ephemeral and it wont last more than 15min of execution, so to keep the data in a persistent way, you'll need to save it for example in a S3
 - /tmp
   - Temporary Storage
   - Like a cached file system can be accessed by multiple invocations
   - data is not persistent
 - Additional libraries in your deployment zip file
   - you have a package Lambda max size of 250mb
   - it slows down deployment 
 - Lambda Layers
   - for Libraries and SDKs in best practice
   - large dependencies
   - better performance 
   - Negative: you cant update your Layer, you must upload your new files and create a new layer
 - S3
   - Object Storage Only
   - Cannot Append Data
   - Every change of data creates a new version of it
 - EFS (Elastic Fule System)
   - Shared File System, persistent and can be dynamically updated
   - Mountable, it can be mounted to other lambdas
   - it must have SAME VPC as the Lambda

### Lambda Environment Variables
is a configuration which allows to set Key:Value items to be retrieved by the Lambda at execution, so this helps to abstract the critical information from being hard coded.\
The Environment Variables are attached to a version so when updated, they will be erased

### Other Lmabda Configurable Parameters
 - VPC
 - General Configuration
 - Triggers
 - Permissions
 - Function URL
 - Tags

### Lambda X-RAY
when Enabled, it allows to disect the different interactions of a lambda with the multiple services of AWS in order to see exactly the actions that are executed inside the Lambda and see the results and errors in a more understandable way

### Lambda Invocation Lifecyle and Errors
by default when a function returns an error, it will perform two retries, first it will wait one minute, then two minutes.

#### Dead-Letter Queues (DLQs)
its a Failure Handlign mechanism that holds failed events until they are retrieved using the Services SQS, SNS or Lambda Destinations.\
For example if an invocation was successful it will send a message to a SQS, but if it failed it will send a message to SNS which will send an email to notify about the failure with a lot of information to understand the error
  

## Characteristics of Event-Driven Architecture
 - Asynchronous: events and communication are connected via triggers but no response is expected or required
 - Loosely Coupled: services and components operate and scale independently of each other
 - Single-Purpose Functions: stateless functions performing a short-lived task
 - AWS Services: are like building blocks
 - Minimal Maintenance: AWS take care of upgrading and make your services reliable
 - Best Practice: Avoid coding what AWS Services already proposes

The normal architecture pipeline goes as following:\
Event Source &rarr; Event Router &rarr; Event Destination\
for example:\
S3 Action &rarr; EventBridge Trigger &rarr; Lambda Execution

### Banking Application Example Architecture

API Gateway &rarr; Lambda Withdrawal &rarr; DynamoDB &rarr; EventBridge &rarr; A/B\
A: &rarr; Lambda Overdraft Increase &rarr; SQS\
B: &rarr; Lambda Overdraft Notification &rarr; SNS

### Image Processing Example Architecture

S3 UploadedImages &rarr; Lambda ImageProcessing &rarr; A/B\
A: &rarr; S3 ProcessedImages &larr; CloudFront\
B: &rarr; DynamoDB

### Processing Streaming Data Example Architecture
SocialMediaEvents &rarr; Kinesis Firehose &rarr; Lambda Data Processor &rarr; DynamoDB

## Step-Functions
Provides a visual interface for a serverless application, which later creates a State Machine to execute the app. It allows the orchestration and manage the logic of your application including:
 - Sequencial Worflow DAG
 - Error Handling
 - Retry Logic

#### Example for Parallel Steps
Start &rarr; Process photo &rarr; IN\
*IN &rarr; Extract Metadata &rarr; OUT*\
*IN &rarr; Resize Image &rarr; OUT*\
*IN &rarr; Facial Recognition &rarr; OUT*\
OUT &rarr; Load in Database &rarr; End

#### Branching Workfow
Start &rarr; Trigger CodeBuild Build &rarr; Get Test Results &rarr; All Tests Passed? &rarr; A/B \
&rarr; A &rarr; Notify Success &rarr; End\
&rarr; B &rarr; Notify Failure &rarr; End

### Step-Function Demo
 - Create a Step-Function and select the Job Poller template with premade Lambdas
 - Check the Open in a new browser tab and Start Execcution
 - Check the Step Function, green are Tasks OK, blue are Tasks in progress and red are Tasks KO
 - Check the Event Schedule

### Comparing Step-Function Workflows
#### Standard Workflows
 - Long-Running, durable and auditable workflows that may run up to a year with 90 days history
 - At-Most-Once Model where tasks are not repeated unless declared by the workflow
 - Non-Idempotent Actions so when processing a payment, only one will be processed not multiple times
#### Express Workflows
 - Short-Lived up to 5 minutes for high-volumes or event-processing-type-workloads
 - Idempotent like transforming input data and storing result in DynamoDB
 - Concurrent executions in parallel
 - Good if an Identical Request has no side effect
 - Types:
   - Asynchronous: an email, you send a message and you continue
   - Synchronous: a conversation, each person takes turns to talk to continue the conversation
 

## X-RAY
You can use it with many AWS Services to see the all interactions of your applications, here is a list of some services:
 - EC2
 - ECS
 - Lambda
 - Elastic Beanstalk
 - SNS
 - SQS
 - DynamoDB
 - ELB
 - API Gateway

Integrate with your apps and use X-ray more dynamically on apps written in:
 - Java
 - Node.js
 - .NET
 - Go
 - Ruby
 - Python

**It is necessary to have the X-RAY SDK and or X-RAY Daemon for the service to be able to capture the information and send it to X-RAY HTTP requests, for ECS run the X-RAY Daemon in its own docker**

You can also personalize your X-ray annotations with Key:Value pairs in order for you to filter information as desired


# DynamoDB
Fast and Flexible NoSQL Database, consistent and single-digit millisecond latency at any scale.\
It is fully managed and it supports a Key:Value data models.\
It supports the following Document Formats:
 - JSON
 - HTML
 - XML

It is great because it can interact with Lambdas and also it can also be Scalable. It offers different Features such as:
 - Performance: SSD Storage
 - Resilence: Spread accross 3 geographically distinct data centers
 - Consistency: 2 leveles are offered:
   - Eventually consistent reads (default): all copies of data reache the other data centers in a second **Best for red performance**
   - Strongly consistent reads: a strongly consistent read always reflects all successful writes **Best for read consistency**
 - ACID transactions: Atomic, Consistent, Isolated, Durable

### DynamoDB Primary Keys

#### Partition Key

Its a unique attribute, the key is input to an internal hash function which determinesthe partition or physical location where the data is stored, **if used as primary key there cant be two same values**

#### Composite Key

 - Partition Key + Sort Key (e.g. forum posts): combination of User_ID + Timestamp (sort key)
 - A Unique Combination 
 - Storage of values as key

### DynamoDB Secondary Index
Query based on an attribute that is not the primary key using *global secondary indexes* and *local secondary indexes*.\
A secondary index allows you to perform fast queries on specific columns in a table rather than on the entire dataset.\
It can only be created when you're creating the DynamoTable, you cannot add, remove or modify later, However:
 - Local Seconday Index
   - Same partition key and different sort key to your table
   - Must be created when you create your table
 - Global Secondary Index
   - Different partition key and fifferent sort key to your table
   - Can be created any time

### DynamoDB Scan vs Query
 - Query: You can query exactly what you need
   - More efficient
   - Removes unwanted data
 - Scan: Scans the entire data for the conditions you've entered
   - Takes more time the bigger the table becomes
   - Can use up the provisioned throughput for a large table in a single operation
Good practice advice is to always limit the amount of data extracted, by setting the page-size parameter

### DynamoDB API Calls
Its a programmatically approach to interact with DynamoDB, for example, the AWS CLI commands actually calls the Dynamo API to interact with the Service. **It is important to remember that you can set AMI permissions to allow certain actions to be executed on your DynamoDB**:
| AWS CLI COMMAND | USAGE |
| ------ | ------ |
|create-table|**Creates** a new table|
|put-item|Adds a **new** item into a table or **replaces** an old item with a new one|
|get-item|Returns a set of **attributes for an item** with the given primary key|
|update-item|Allows you to **edit** the attributes of an existing item (e.g. **add or delete** attributes)|
|update-table|Used to **modify** a table (e.g. modify the provisioned throughput settings of the table)|
|list-tables|Returns a **list og tables** in your account|
|describe-table|Returns **information about** the table|
|scan|Reads every item in a table and returns **all items** and attributes. Use `FilterExpression` to return fewer items|
|query|Queries the table based on a **partition key** value that you provide|
|delete-item|Allows you to delete an item based on its **primary key**|
|delete-table|**Deletes** a table including all of its items|

### DynamoDB Access Control
 - IAM
 - IAM Permissions
 - IAM Roles
 - Restricting User Access: using IAM condition to restrict user access to only their own records (e.g. Mobile Games where a player needs to see his score or items but not other players info)
   - Using the `dynamodb:LeadingKeys` permission policy allows users to access only the items where the partition key value matches their User_ID

### DynamoDB Provisioned Throughput
DynamoDB is measured in **Capacity Units**:
 - Specify Requirements: at the creation of the table you can specify the amount you need for reading or writing capacity
 - Write Capacity Units: 1 write capacity unit = 1Kb write per second
 - Read Capacity Units: 1 read capacity unit = 1 strongly consistent read of 4KB per second **OR** 2 eventually consistent reads of 4kb per second (**default**)  

To calculate how much you need just do:
1. divide the size of an item by the kb of the Capacity Unit
1. round up to the nearest integer
1. multiply by action per seconds
1. in Reading, if not consistent then divide by 2

### DynamoDB On-Demand Capacity
It can instantly scales up and down based on the activity of your application, great for Unpredictable workloads, New applications you dont know the use pattern yet or When you want to pay for what you use.

### DynamoDB Accelerator (DAX)
Fully managed, clustered in-memory cache for DynamoDB. Delivers up to a 10x read performance improvement. Microsecond performance for millions of requests per second. Great for Read-Heavy Workloads:
 - Auctions applications
 - Gaming
 - Retail sites during BlackFriday

For writing, it writes the operations directly to the Cache DAX Cluster and also to the DynamoDB at the same time.

### DynamoDB Streams
Streams are the Logs or Time Ordered Sequences to keep a backup of operations that have been executed in the DB. It has a dedicated endpoint for access and its useful for Audits or archive transactions. They are saved in near real-time and they can last for 24h.\
Great for Lambda Event Source, so that it can trigger Lambdas based on events that happened in the DynamoDB.

### DynamoDB Provisioned Throughput Exceeded
If the Reads or Write request rate is too high, you can set up an **Exponential Backoff** in order to increase the amount of time between every retry attempt. In AWS CLI it will automatically retry until successful.

So keep in mind if you get a `ProvisionedThroughputExceededException`, which means that there are too many requests. If after 1 minute this doesn't work, your request size may be exceeding the throughput for your read/write capacity.

### DynamoDB Create a Table Demo
In this demo the use case is to create an IAM User with DynamoDB access, create an EC2 instance for AWS CLI configuration, then the creation of a DynamoDB Table:
 - In IAM create a new user called "dynamoDBAdmin" with permission policy **AmazonDynamoDBFullAccess**
 - Once created, retrieve the Access Key ID and the Secret Access Key to configure the AWS CLI (you can download the info as csv)
 - In EC2 create a new instance, check that Auto-assign Public IP is enabled to be able to ssh, in User Data lets add: 
 ```
 #!/bin/bash
 yum update -y
 yum install git -y
 ```
 - Create a new key pair for SSH and finish the creation of the instance
 - In your local machine, find the downloaded `.pem` file for accessing the EC2 machine in ssh, perform `chmod 400 KEY_NAME.pem`
 - SSH into the machine `ssh ec2-user@EC2_PUBLIC_IP -i KEY_NAME.pem`
 - Now configure the AWS CLI using the User Access Key and Secret Key
 - copy paste the [command to create the S3 table](https://github.com/ACloudGuru-Resources/course-aws-certified-developer-associate/blob/main/Create_A_DynamoDB_Table_Demo/DynamoDB_Commands.txt) to the command line
 - Add [new items](https://github.com/ACloudGuru-Resources/course-aws-certified-developer-associate/blob/main/Create_A_DynamoDB_Table_Demo/items.json) into the Dynamo table by using git `git clone https://github.com/ACloudGuru-Resources/course-aws-certified-developer-associate.git` and find the `Create_A_DynamoDB_Table_Demo/items.json`
 - Using AWS CLI we can add them to DynamoDB `aws dynamodb batch-write-item --request-items file://items.json` 
 - You should receive the output `"UnprocessedItems":{}` which means everything went OK
 - Check an iteù from the table `aws dynamodb get-item --table-name ProductCatalog --region us-east-1  --key '{"Id":{"N":"403"}}'`
 - Access the AWS Console DynamoDB interface, and in Tables select your new table and *Overview* and then *Items*
 - Go to Create Item, append a Description, Price, Color, Size, ProductCategory and Save, check the new item
 - Run a Query with an ID number 403 and see you result, also a Scan to see the results
 - End by Deleting the Table

### DynamoDB TTL Demo
Its for the configuration of the TimeToLive of data in your DynamoDB, this way it will marked for deletion one its time expires. It uses **Epoch Time** to the creationTime and TTL
 - Enable IAM DynamoDBFullAccess to the user you'll use
 - [Create a table and populate it](https://github.com/ACloudGuru-Resources/course-aws-certified-developer-associate/tree/main/DynamoDB_TTL_Demo)
 - Check the Items, and then select the **Enable TTL**
 - While configuring the TTL, you can choose the condition and check the Preview

# KMS and Encryption in AWS
Key Management Service makes it easier to encrypt in AWS when dealing with sensitive information:
 - Managed Service: makes it easy to create and control the encryption keys
 - Integrated: many AWS Services are compatible with KMS and it can be enabled by just checking a box
 - Simple: easy encryption with the keys that you manage

One type of key is called a CMK, Customer Master Key, which can encrypt/decrypt data **up to 4kb**.

The other type of key is called Data Key, which allows for encryption of a bigger amount of data, this encryption is also known as **Envelope Encryption**.

### KMS Envelope Encryption
**Encryption for data >4kb** where the data is encrypted with the CMK, then the encryption key is also encrypted and put together with the data.

Therefore for Decryption, the data is envelopped with the Data Key and so the CMK allows to decrypt the DataKey and then with it, decrypt the Data.

Encryption of data using the CMK directly because:
 - Network: when you encrypt data directly with KMS it must be transferred over the network
 - Performance: with envelope encryption, only the data key goes over the network, not your data
 - Benefits: the data key is used locally in your application, so it avoids the needs to transfer large amounts of data to KMS

### KMS Key Rotation
It allows to automatically rotate the key every year, in order to make sure that one same key doesnt have the power to decrypt all.\
It saves **previous versions** of the keys in order to decrypt the files that where previously encrypted

### KMS Creation Demo
How to create a KMS in AWS Console:
 - In IAM, create a group and give it `AdministratorAccess`
 - Create Users ***KeyManager*** and ***KeyUser*** and add them to the group we just created
 - Go to KMS, then Customer Managed Keys, go with defaults, put an Alias, give Admin Permission to ***KeyManager***, Usage Permission to ***KeyUser*** and create the key
 - Revise the Key Policy and finish

# AWS Certificate Management (ACM)
Its a service that allows to create and manage public and private Secure Socket Layers (**SSL**) / Transport Layer Security (**TLS**) certificates.

Can be used with AWS Services like Elastic Load Balancing, CloudFront, API Gateway and web applications.

### SSL TLS Certificates
Are Digital certificates to verify the authenticity of a website, it enables a secure connection between visitors and the website.

Theyre used during the encryption process to encrypt data in transit.\
