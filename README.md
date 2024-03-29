# learn_aws_associate_developer
repo for adding interesting codes and documenting the information for the certification

# starting date 22 January 2024
according to the website, for 24h per week of learning, the candidate is considered ready for presenting the exam the **5 of february**

# IAM

### Users
There are:
 - Users: which we can add specific policies
 - Groups: add users to one or multiple groups, each group having a set of permissions
 - Roles: Give a role to a service or person to have a set of permissions depending on their execution

### Login
there exists:
 - MultiFactorAuthentication
 - single URL login for the different users, all they need to do is go to the link and put their login and password
 - It is also possible to set rules to the passwords like complexity, length and expire time

### Best Practices
Always give the least access possible to the different Users/Groups
It is also recommended to add Users to a Group so that its easier to control many people at one

## Cross-Account Access
Delegate access to resources in different AWS accounts that you own.

#### Manage Resources in Other Accounts
Share resources in one account with users in a different account.

#### IAM Role
Create a role in one account to allow access and grant permissions to users in a different account.

#### Switch Roles
Switch roles within the AWS management console. No Password is required.

#### Example Use Case
A company has a Dev account and a Prod account. Developers need to get some production data in order to work on a new feature, they already have full Dev account permissions but it they would also need ReadOnly access to a S3 bucket in the Prod account.

In the Dev account, create a Developers IAM Group, then the IAM Policy to allow reading S3 in the Prod account.\
Follow by creating an IAM Role in Prod including the Account_ID of Dev that points to the IAM Policy in Prod.\
Finally create an IAM Polcity in Dev that points towards the IAM Role of Prod.

## Web Identity Federation
Simplifies authentication and authorization for web applications:

#### UserAccess to AWS Resources
Users access AWS resources after successfully authenticating with a web-based identity provider like Facebook, Amazon or Google (a JWT is provided from the web-base identity provider to AWS).

#### Authentication
Following successful authentication, users receive an authentication code from the web ID provider.

#### Authorization
Users can trade this authentication code for temporary AWS security credentials, authorizing access to AWS resources.

## Amazon Cognito
Provides web ID federation, including sign-up and sign-in functionality for you applications, and access for guest users which **map to IAM Roles**:
 - **Identity Broker**: manages authentication between your app and webID providers
 - Multiple Devices: synchonizes user data for multiple devices
 - **Recommended for Mobile**: best use for mobiles when calling AWS services

#### User Pools
User directories used to manage sign-up and sign-in functionality for mobile and web applications.

#### Sign-in
Users can sign-in directly to the User Pool, or using Facebook, Amazon or Google (a JWT is provided from the web-base identity provider to AWS).

#### Identity Pools
Identity Pools enable you to provide **temporary AWS credentias**. Enabling access to AWS services like S3 or DynamoDB

### Cognito Push Synchronization
 - Devices: cognito tracks the association between the user identity and the varios devices he uses
 - Seamless: it  upddates and synchronizes user data across multiple devices
 - SNS Silent Notification: SNS notification to all devices associated with a given user identity whenever data stored in the cloud changes

## AWS Policies

### AWS Managed Policies
An IAM policy created and administered by AWS like `AmazonS3FullAccess`:
 - Assign appropiate permissions to your users without having to write the policy yourself
 - Attach to multiple users, groups or roles in the same AWS account or across different accounts
 - You cannot change the permissions defined in an AWS managed policy

### Customer Managed Policies

#### Created by You
A standalone policy that you create and administer inside your own AWS account. You can attach this policy to multiple users, groups, and roles within your own account.

#### Copy an Existing Policy
In order to create a customer managed policy, you can copy an existing AWS managed policy and customize it to fit the requirements of your organization.

#### Your Needs
Recommended for use cases where the existing AWS managed policies dont meet the needs of your environment.

### Inline Policies 

#### 1:1 Relationship
There is a strict 1:1 relationship between the entity and the policy.

#### Embedded
When you delete the user, group, or role in which the inline policy is embedded, the policy will also be deleted

#### Single User, Group or Role
In most cases, **AWS recommends using managed policies over inline policies**; However it is useful when you want to have a sinle User, Group or Role.\
The policy must not be inadvertently assigned to any other user, group, or role than the one for which it is intended.\
The policy must only ever be attached to a single user, group or role.

## Security Token Service STS AssumeRoleWithWebIdentity 

#### STS API
`assume-role-with-web-identity` is an API provided by STS (Security Token Service)

#### Temporary Credentials
Returns temporary security credentials for users authenticated by a mobile or web application or using a web ID provider like Amazon, Facebook, Google, etc... (a JWT is provided from the web-base identity provider to AWS)

#### Web applications
Regular web applications can use the `assume-role-with-web-identity` API. **For mobile applications Cognito is recommended**

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
 ```bash
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

# SQS Simple Queue Service
It was the first public Service from AWS. Its main objective is to store received messages in a queue, then distribute them one by one to another Service for each message to the consumed in a specific way.

For example, lets say you have an S3 bucket that will receive many new images, each new image will trigger a notification to invoke a Lambda which will create a message, e.g. JSON, and will be sent into a SQS. Later an EC2 will consumet the messages one by one, in this case, transforming the image into higher resolution which can take some time. So the SQS ensures that there is a list of files to treat and if there are more incoming, they will be added to the queue.

It is a **pull-based system**, where the Service at the end of the SQS will pull for new messages to process. Each message is stored in the SQS, as soon as the end-point Service pulls the message, the message will be invisible and exclusive to the Service to be processed, if it takes too much time to process or if there is an error, then the message will become visible again to be processed later.

SQS Is great for **Decoupling Application Components** when you have Services that work together in one sense, but one of them is way faster than the other and so make them more independent.

Messages can be:
 - XML
 - JSON
 - Unformatted text
 - 256Kb in size
 - Retention up to 14 days in the queue
 - Default retention is 4 days

### SQS Types
 - Standard Queue: 
   - Unlimited transactions
   - Guarantee that a message will be delivered at least once
   - Best-Effort Ordering
 - FIFO Queue: 
   - First-In-First-Out Delivery
   - Exactly-Once Processing, **No chance of duplicates**
   - 300 transactions per second (TPS)
   - Same other capabilities as the Standard Queue

### SQS Settings

#### SQS Visibility Timemout
Is the amount of time that the message is invisible in the SQS queue after a reader picks up a that message. Ideally the job will be processed before the visibility time out expires, and the message will be deleted from the queue. If the job is not processed within that time, the message will become visible again and another reader will process it. **Default is 30 seconds, Max is 12 hours**

#### SQS Short Polling VS Long Polling
 - Short Polling:
   - Returns a response immediately even if the message queue is being polled is empty
   - This can result in a lot of empty responses if nothing is in the queue
   - You will still pay for these reponses
 - Long Polling:
   - Periodicaly polls the queue
   - Doesnt return a response until a message arrives in the message queue or the long poll times out
   - **Can save money**
   - **Long Polling is generally preferable to Short Polling**

#### SQS Delay Queues 
 - Postpone delivery of new messages to a queue for a number of seconds
 - Messages sent tot he Delay Queue remain invisible to consumers for the duration of the delay period
 - D**efault delay is 0 seconds, maximum is 900**
 - For Standard Queues, changing the setting will only affect the new messages arriving to the queue
 - For FIFO Queues, this affects the delay of messages already in the queue 
 - Use Case of adding a delay can be to allow for updates to your sales and stock control databases before sending a notification to a customer confirming an online transaction

#### SQS Managing Large SQS Messages
Best practice for managing large SQS messages using S3:
 - For large SQS messages - **256Kb up to 2GB in size**
 - Use S3 to store the message
 - Use Amazon SQS Extended Client Library for Java to manage them
 - (You'll also need the AWS SDK for Java) - provides an API for S3 bucker and object operations
   - Specify that messages are always stored in Amazon S3 OR only messages >256Kb
   - Send a message which references a message object stored in S3
   - Get a message object from S3
   - Delete a message object from S3

# SNS Simple Notification Service
Its a web service that makes it easy to set up, operate, and send notifications from the Cloud. Messages sent from an applicaiton can be immediately delivered to subscribers or other applications, This is considered a **Push-based system**. Here are some of the notifications that can be done:
 - Push notifications to devices
 - SMS and Email
 - Trigger an AWS Service like a Lambda with a message payload

It works using a **Pub-Sub model** which is a publish and subscribe model; Applications *PUBLISH* or *PUSH* messages to a *TOPIC*; Subscribers *RECIEVE* messages from a *TOPIC*; SNS will also format the message so that the messages appears in the correct format of the receiver (Android, iOS, HTTP, etc.).

### SNS Durable Storage
AWS Prevents SNS from being lost, all messages are published to SNS are stored redundantly across multiple Availability Zones.

### SNS Benefits
 - Instantaneous
 - Simple with easy APIs and easy integration 
 - Flexible message delivery over multiple transport protocols
 - Inexpensive, pay-as-you-go with no up-front costs
 - Easy to configure
 - Manages Services with high availability and durability for production environment

|SNS|SQS|
|----|----|
|Messaging Service|Mesasging Service|
|SNS is push-based|SQS is pull-based|
|Think push notifications|Think polling the queue for messages|

# SES Simple Email Service
For sending emails to a list of email addresses; SES can be used for incoming and outgoing mail, and it is not subscription-based, just email address based.

|SES|SNS|
|-|-|
|Email messaging service|Pub/Sub messaging service; formats include SMS, HTTP, SQS, email|
|Can trigger a Lambda function or SNS notification|Can trigger a Lambda function|
|Can be used for both incoming and outgoing email|Can fanout messages to a large number of recipients|
|An email address is all that is required to start sending messages|Consuemrs must subscribe to a topic to receive notifications|

# Kinesis
**A family of services** that enables you to collect, process, and analyze streaming data in real time.

### Kinesis Streaming Data
Data that is generated constinously by many sources in small sizes (kilobytes) and generally unformatted data like:
 - Financial transactions
 - Stock prices
 - Game data
 - Social media feeds
 - Location-tracking data
 - IoT sensors
 - Clickstream data
 - Log files

### Kinesis Streams
Its divided into **Data Streams** and **Video Streams** which allow to process data in real-time.

#### Kinesis Data Streams
Producers send data to Kinesis Streams and they can be kept in Kinesis Streams for a **default 24h or max 353day retention**; the data is first gathered in what are called **Shards** which are a **Sequence of data records** and provides a fixed unit of capacity:
 - Five reads per second
 - Max total read rate is 2MB per second
 - 1000 writes per second
 - Max total write rate is 1MB per second

For consumers like the EC2 instance, the Kinesis Client Libraries allow for:
 - Tracking and listing the number of shards and also new ones
 - Ensures that for every Shard there is a **record processor**
 - Manages the number of record processors relative to the number of shards & consumers
 - For multiple consumers it will automatically load balance a portion of the processor to each instance

For Best Practice:
 - Ensure that the number of instances do not exceed the number fo shards (except for failure or standby purposes)
 - One worker can process multiple shards
 - CPU utilisation is what should drive the quantity of consumer instances you have **NOT** the number of shards
 - Use an Auto Scaling group, and base scaling decisions on CPU load on your consumers

#### Kinesis Video Streams
Same as Data Streams but if it has **VIDEO** then it is strictly **VIDEO STREAMS**

We can create consumers like EC2 machines, Lambdas, etc.. to treat the data and for example format it or clean it, then they can export the final data into a database service or S3  

### Kinesis Data Firehose
**Capture**, Transform and **Load** data streams into AWS data stores to enable near real-time analytics with BI tools.

There is no data retention, the data is sent directly to S3 or you can configure for a Lambda to be the step before the S3 in order to transform the data before it is deposited

### Kinesis Data Analytics
Analyze, **Query**, and Transform streamed data in real-time using **Standard SQL**. Store the results in an AWS data store.

It is a top layer Kinesis service which allows to analyze the data using SQL that comes from other Kinesis Services and store it in AWS (e.g. S3, Redshift, OpenSearch)

# Elastic Beanstalk
Deploy and Scale Web Applications:
 - Supported Languages: Java, .NET, PHP, Node.js, Python, Ruby and Go
 - Supported Platforms: Apache Tomcat, Docker
 - Developer Benefits: Focus on writing code and not worrying on the infrastructure needed to run the application, auto Scaling!

Elastic Beanstalk is also capable on creating all the necessary AWS Services declared for your application in order to create the necessary architecture, Health Monitoring is available for verying the status of your application.

Finally, You are in administrative control of the AWS Resources and there is no additional charge, just the normal price of the used Services.

### Elastic Beanstalk Update

#### All at once 
Deploys to all hosts concurrently, you will experience a total outage; Not ideal for mission-critical production systems.\
If the update has errors, you'll have to rollback and redeploy the original version to all the instances.\
Only useful for test or dev environment since its more quick to drop and create.

#### Rolling 
Deploys the new version in batches, each batch is taken out of service while the deployment takes place.\
Your environment capacity will be reduced by the number of instances that are updating.\
Not ideal for performance sensitive systems since your total performance capacity will lower while the update takes place which is longer, also if the new version is wrong the rollback will be slow too.

#### Rolling With Additional Batch
Launches an additional batch of instances, then deploys the new version in batches. This update method allows to keep the same performance since new instances are created, it also keeps some of the instances in the first version.\
Great for keeping same performance while updating, never an outage moment.\
In case of a bad update, then the rollback will take time and performance will decrease.

#### Immutable 
Deploys the new version to a fresh group of instances, health status are checked and then if all ok, it deletes the old instances.\
So in case of a bad update, the old instances are kept and the bad ones erased.\
Great for critical mission systems.

#### Traffic Splitting 
Installs the new version like Immutable deployment but it redirects only a percentage of the traffic towards the new versions for evaluation, useful for testing a new version before doing a full Update, also known as **Canary Testing**

### Elastic Beanstalk Advanced

#### Pre-Amazon Linux 2 Elastic Beanstalk Configuration Files
 - Configuration File: define packages to install, creates Linux users, enable services and configure load balancers
 - YAML or JSON files
 - Constraints: file must have a `.config` extension and be inside a folder called `.ebextensions` in the top-level directory of your application source code bundle

#### Amazon Linux 2
 - Buildfile: commands that run for short periods, and then **exit** upon task completion
   - the Buildfile must be in the **root** directory of your application source
   - the format is `<process_name>:<command>`
 - Procfile: **Long-running** application processes, like a command to start and run the application
   - the Procfile must be in the root directoy of your application source
   - the formant is `<process_name>:<command>`
   - Elastic Beanstalk expects Procfile tasks to run **continuously**
   - It **monitors** and **restarts** any processes that terminate
 - Platform hooks: Custom script or executable files for Elastic Beanstalk to run at a **chosen stage** of the EC2 provisioning stage
   - Stored in **dedicated directories** in your application source code
   - `.platform/hooks/prebuild` files to run **before** it build, sets up, and configures the application and web server
   - `.platform/hooks/predeploy` files to run **after** configuring the application and webserver but **before** it deploys to the final runtime location
   - `.platform/hooks/postdeploy` files to run **after** everything else

### Elastic Beanstalk RDS launching configuration

#### Option 1: Launch RDS within Elastic Beanstalk
 - If you terminate the environment, the database will also be terminated
 - Good option for Dev and Test deployments, bad for production

#### Option 2: Launch RDS outside of Elastic Beanstalk
 - Allows to tear down the application environment without affecting the database instance
 - Better for Production systems since the data will not be affected

#### Connecting to an Outside Database
 1. An additional Security Group must be added to your environment Auto Scaling group
 1. You'll need to provide connection string information to your application servers using Elastic Beanstalk environment properties

### Elastic Beanstalk Migration Application

#### Migration Assistant Tool
If you have for example a .NET application running on Windows servers in your data center and you want to **migrate** to AWS and run it in Elastic Beanstalk.

**.NET Migration Assitant** (public repo) enables to migrate a **.NET** application or your **entire website** from Windows to AWS Elastic Beanstalk with Interactive PowerShell Utility

# CI CD
**Continuous Integration and Continuous Deployment**:
 - Automatises the integration and deployment of the application when there are chagements in the code
 - Manual changes are bad, they are prone to mistakes and makes it harder to detect and it cant scale up
 - Small incremental changes so that it can be tested and debug in a more simple way
 
### Continuous Integration Workflow
 - Shared Code Repository were developers can share or merge code changes
 - Automated Build when changes are detected in the code
 - Automated Test of new changes in code to ensure quality
### Continuous Delivery and Deployment Workflow
 - Code is merged after successful tests, it integrates the master repository
 - Manual decision, humans may be involved in the decision to deploy the code, **Continuous Delivery**
 - Prepared for deployment, the code is buimt, tested and packaged for deployment
 - Fully Automated, everything can be automated, even the decision for deployment, **Continuous Deployment**

## CodeCommit 
Centralized Code Repository, Source and Version Control of code, allows teams to collaborate on code and files of a project, **Continuous Integration**
## CodeBuild
Automated Build of code, it compiles and run tests, then packages the code that is ready to deploy, **Continuous Delivery**
## CodeDeploy
Automated Deployment, automates code deployment to any instance like EC2, Lambda and on-premises, **Continuous Delivery**.

The **AppSpec File** is the configuration file used by EC2 and on-premises as YAML, and used by Lambda as YAML/JSON. Its structure is:
 - version
 - OS
 - files: `/Scripts`, `/Config`, `/Source`
 - hooks

The Run Order for an in-place Deployment is:
 1. BeforeAllowTraffic: tasks to run on instances **before** they are de-registered with the Load Balancer
 1. AllowTraffic: register instances with a load balancer
 1. AfterAllowTraffic: tasks you want to run on instances **after** they are registered with a Load Balancer

The Workflow steps are:
 1. Application Stop
 1. Download Bundle
 1. Before Install instructions
 1. Install
 1. After Install instructions
 1. Application Start
 1. Validate Service run tests

## CodePipeline
Manages the Workflow, end-to-end solution, build, test and deploy your application every time there is a code change, **Continuous Deployment**. There are two type of deployments, In-Place and Blue/Green.
|In-Place|Blue/Green|
|---|---|
|Updates the existing instances one by one|Creates the same amount of instances but with the new version of code to work in parallel|
|Capacity is reduced during deployment|No capacity reduction|
|Lambda is not supported|Green instances can be created ahead of time|
|Rolling back involves a re-deploy|Easy to switch between old and new|
|Great when deploying the first time|You pay for 2 environments until you temrinate the old servers|

The example CodePipeline Workflow is:
 1. CodePipeline: the workflow is defined and begins when there is a change detected in your source code
 1. CodeCommit: New source code appears in the repository
 1. CodeBuild: The code is compiled, tested and packaged
 1. CodeDeploy: The application is deployed into dev or prod environment

## CodeArtifact
A central repository that can be used by all your developers to obtain the correct version of the software packages required for their projects. (like python package Index or npm Registry).

Artifacts can be:
 - Documentation
 - Compiled applications
 - Deployable packages
 - Libraries

CodeArtifact allows to:
 - Make Third-Party Software **available** for use
 - IT leaders can make **approved** packages available, like those that are **supported**
 - Developers know where to **find** approved packages and also **publish and share** their own packages

A **CodeArtifact Domain** is declared with a **Repository** and an **Upstream Repository**.\
The **Domain** has **External Connection** to the **Public Repo** to allow for new versions.

# ECS Elastic Container Service
Its the Service for managing Containers which are virtual operating environments. They are Standardized which everything the sofware needs to run their projects. Microservices applications can be created from containers in order to facilitate the Scaling according to demand.

A DockerContainer has:
 - Code
 - Libraries
 - Virtual Kernel

Which is loaded by Docker in an Operating System which make your application:
 - Highly Scalable
 - Fault Tolerant
 - Easy To Mantain

ECS is a container orchestation service which supports Docker and Windows Containers. Its similar to Kubernetes but with deep integration with AWS services.

ECS can run the containers in a **Cluster of Virtual Machines**, **Fargate for Serverless** or **EC2 for More Control**

AWS Services which use ECS are:
 - Amazon Sagemaker: for deploying and scaling machine learning models for training or prediction jobs
 - Amazon Lex: uses deep learning to build conversational interfaces like chatbots
 - Amazon.com: it runs on ECS

### Elastic Container Registry ECR
Is the public or private registry that contains all the images that are available in public, or the private ones that you upload to AWS.

So ECS connects to ECR to pull the image that you choose, and then deploys the image in containers

# CloudFormation
Its purpose is to help manage, configure, and provision your AWS infrastructure as. Every resource is defined inside a CloudFormation template which then creates the architecture of the project. It supports YAML or JSON.

The benefits are:
 - Consistent, since its a configure file, there is no human error
 - Quick and efficient, less time than doing it manually
 - Version control, keep versions of your architecture and peer review your templates
 - Free to use, just pay the resources created
 - Manage updates, can be used to manages updates and dependencies
 - Rolling Back, you can roll back to a previous state and delete the entire stack as well

Things to remember about CloudFormation:
 - Its Infrastructure as Code
 - Parameters : for custom values
 - Conditions : for privisioning resources based on conditions
 - Resources : to list the resources to create
 - Mapping : to create custom mappings like Region:AMI
 - Transform : to reference code located in S3 for reusable snippets, also for reusing the SAM

### CloudFormation Process
 1. Create the YAML/JSON Template
 1. Upload to CloudFormation using S3
 1. CloudFormation reads the template and makes API calls to create the resources
 1. CloudFormation Stack is the resulting set of resources created

### CloudFormation Export Stack Values
In order to make things easier between different stacks, for example, one stack creates a network VPC and Security Group and a RDS then a second stack will make use of those networks and RDS, so the first Stack can expose the ARN of the those created resources, so that the second Stack can import them to their CloudFormation Template.

Inside the Template, there should be a `Output` Section with the Name of each Element you want to export and in the Key value `Export` write the `resourceID`:
```json
{
  "Outputs": {
    "PublicSubnet": {
      "Description": "ID of the subnet",
      "Value": {
        "Ref": "PublicSubnet"
        },
      "Export": {
        "Name": {
          "Fn::Sub": "${AWS::StackName}-SubnetID"
        }
      }
    }
  }
}
```
### CloudFormation Serverless Application Model SAM
Define and provision serverless applications using CloudFormation
 - `sam package` : Packages your application and uploads to S3
 - `sam deploy` : Deploys your serverless app using CloudFormation

### CloudFormation Nested Stack
In the Template it is also possible to declare other templates/Stacks, this is helpful for making certain staks like modules to be reused.
It is important to declare it inside your Template as:
```yaml
Resources:
  Type: AWS::CloudFormation::Stack
  Properties:
    NotificationARNs:
     - String
    Parameters:
     AWS CloudFormation Stack Parameters...
    Tags:
     - Resource Tag
    TemplateURL: https://s3.amazon.com/.../template.yml
    TimeoutInMinutes: Integer
```
## Cloud Development Kit CDK
Allows for creation of Resources or Stacks using a **programming language** like Python, Go, etc..

### Process
 1. `cdk init` inside an empty directory
 1. `npm run build` to compile the app
 1. `cdk synth` to create the CloudFormation template
 1. `cdk deploy` to deploy the stack using CloudFormation

### Deploy
Applications and AWS Resources.

### Define
 - Apps: **Container** for one or more stacks
 - Stacks: **Unit** of deployment
 - Construct: Defines the AWS **Resources**

### Synthesize
CloudFormation Template creation, use the CDK to create a **CloudFormation Tempalte** from your code and deploy it.

# Amplify
Its a Service that allows **front-end deveopers** create a full stack application with managed Backend end Front end.
 - Frontend Developers: create full-stack web and mobile applications on AWS
 - Amplify Libraries: Integrate with Cognito, S3, Lambda and API Gateway to create a reliable backend
 - Amplify Hosting: Dynamic/Static web hosting that integrates with your code repository for CI/CD 
 - Amplify Studio: Simple visual tool used to confifure both front and back end of the application

# CloudWatch
Is a **monitoring** service to monitor the health and performance of your AWS resources, as well as the application that you run on AWS, and in your own datacenter, the metrics are also stored indefinitely and can be retrieved after the resource has been terminated. It can monitor:
 - Compute
   - EC2 instances
   - Auto Scaling Groups
   - Elastic Load Balancers
   - Route53 health checks
   - Lambda
 - Storage & Content Delivery
   - EBS volumes
   - Storage Gateway
   - CloudFront
 - Databases & Analytics
   - DynamoDB tables
   - ElastiCache nodes
   - RDS instances
   - Redshift
   - Elastic Map Reduce
 - Other
   - SNS topics
   - SQS queues
   - API Gateway
   - Estimated AWS charges

### CloudWatch Agent
Allows you to create your own metrics to measure.

### CloudWatch Logs
Allows you to monitor operating system and application logs

### Cloudwatch and EC2
All EC2 instances send key health and performance metrics to CloudWatch; by default they are:
 - CPU
 - Network
 - Disk
 - Status Check

EC2 does not send operating system-level metrics to CloudWatch; However, it is possible to install the CloudWatch Agent in the EC2 instance, which allows to colelct operating system metrics and send them to CloudWatch:
 - Memory Usage
 - Processes running on the instance
 - Amount of free disk space
 - CPU idle time
 - etc..

#### EC2 CloudWatch Metric Frequency
By default, EC2 sends metric data to CloudWatch in 5-minute intervals.

For an additional charge you can enable detailed monitoring that sends metrics at 1-minute intervals.

For custom metrics, the default is 1-minute intervals, and you can configure high resolution metrics that are sent at 1 second intervals.

### Monitoring System and Application Logs

#### Monitor Log Files
Monitor and troubleshoot your applications using existing system and application log files.

#### Customize For Your Application
Monitor your logs in near-real time for specific phrases, values or patterns. **Requires the CloudWatch agent**

#### Use Case
Track the number of errors that occur in your application log and send yourself a notification whenever the rate of erros exceeds a threshold you specify.

### CloudWatch Alarms

#### Alarms
This include EC2 CPU utilization, Elastic Load Balancer latency, or even the charges on your AWS bill.

#### Thresholds
You can set appropiate thresholds to trigger the alarms and actions to be taken if an alarm state is reacherd.

#### Use Case
You can set an alarm that sends you a notification or executes an Auto Scaling policy if CPU utilization exceeds 90% on your EC2 instance for more than 5 minutes

#### Send Email using SNS Notificaiton topic
You can create an Alarm and configure a topic and email senders where so that when the alarm triggers, the email users will receive an email specifying the error and resource that triggered it.

## CloudWatch Concepts

### CloudWatch Metrics
A metric is a **variable** to **monitor**. Metrics are uniquely defined by a **name**, a **namespace**, and zero or more **dimensions**

Metrics are in time-ordered **Sequence** of **values** or **data points**, which are published to CloudWatch

Each **data point** in a metric has a **Timestamp**, an optionally, a unit of measurement.

For example the CPU usage of an EC2 instance.

### CloudWatch Namespaces
A namespace is a **container** for CloudWatch **metrics**, for example, EC2 uses the **AWS/EC2** namespace; You can create your own namespaces to publish **custom metric** data.

You must **specify** a namespace for **each data point or value** that you publish to CloudWatch.

Specify the namespace name when **create** a metric.

Metrics in **different** namespaces are **isolated** from each other.

Metrics from different applications are **not aggregated** into the same statistics; With **Custom Namespaces** you can send metrics from **different applications** to different namespaces allowing you to **segregate** metrics collected from different applications.

### CloudWatch Dimensions
A **dimension** is like a **filter**. You can filter data by **Name:Value Pair**, also by **InstanceID** and also allows you to **aggregate** data across dimensions for some services (e.g. EC2).

### CloudWatch Dashboard
is a custom view, like a home page that you create in order to monitor what you want. So it helps for checking the health of your critical systems and applications, just select metrics that are important to you and add them to the dashboard.

## CloudTrail
Its for recording user activity in your AWS account, wether if its from the CLI or console, also record activity related to creation, modification or deletion of resources, and even failed authentification.

Records API calls for your AWS account and delivers log files containing API calls to an S3 bucket. Which can be integrated with CloudWatch Logs to monitor any desired activity.

### To remember between CloudWatch and CloudTrail
|CloudWatch|CloudTrail|
|--|--|
|Monitors performance and metrics|Records API calls for you AWS account|
|CloudWatch Logs|API activity history related to creation, deletion and modification of AWS resources|
|CloudWatch Alarms|Do you need an audit log of user activity in your AWS account?|
|Do you need to monitor the performance of AWS resources?||

## CloudWatch Actions

### CloudWatch API
Supports a long list of different **actions**. These actions allow you to **Publish, Monitor and Alert** on a variety of metrics. These are particularly powerful when creating custom metrics for **monitoring and alerting** for your application.

Two commonly used actions are **PutMetricData and PutMetricAlarm**

#### PutMetricData
```sh
aws cloudwatch put-metruc-data \
--metric-name PageViewCount \
--namespace MyService \
--value 25 \
--timestamp 2022-01-30T12:00:00.000Z
```

#### PutMetricAlarm
```sh
aws cloudwatch put-metric-alarm
--alarm-name PageViewMon \
--alarm-description "PageView Monitor" \
--metric-name PageViewCount \
--namespace MyService \
--statistic Average \
--period 300 \
--threshold 50 \
--comparison-operator GreaterThanThreshold \
--evaluation-periods 1
```
## CloudWatch Logs Insight
Interactive query and analysis for data stored in CloudWatch Logs. Bespoke query language, query and filter logs directly, generate vizualisations.

# EventBridge
Is all about **event-driven architecture**. An event is a change in state and its used for triggering targets depending on the declared rules.

Even though it sounds similar to CloudWatch Events, EventBridge is the **preferred way** to manage your events. CloudWatch Events and EventBridge are the **same underlying service** and API, but EventBridge provides more features.

Changes you make in either CloudWatch or EventBridge will **appear in each console**.

Use case: Your company requires that all EC2 instances have encrypted disks. AWS Config detects that somebody has created a new instance without encrypting the attached EBS volumes. An event is generated and sent to EventBridge, which triggers a rule that invokes an action to send you and email using SNS

#### Scheduled Events
EventBridge **rules** can also run on a schedule. e.g. Once an hour or day, or using a **cron expression**, we can set a rule to run at the same time on a specific day, week or month.

# Recognizing Common HTTP Error Codes

## Client-Side Errors
### 4XX 
|Error Code|Name|Meaning|
|--|--|--|
|400|Access Denied Exception|You dont have the required access|
|403|Missing Authentication Token|The request did not contain a valid X.509 certificate or AWS access key ID|
|404|Malformed Query String|The query string contains a syntax error or Object doesnt exist or file not found|
## Server-Side Errors
The request was valid, but the server was not able to fulfill it. This errors could appear also when there is really high traffic and the servers is having difficulty treating all the demand.
### 5XX
|Error Code|Name|Meaning|
|--|--|--|
|500|Internal Failure|The request failed due to an unknown error, exception or failure|
|503|Service Unavailable|The request failed due to a temporary failure of the server|

# Recognizing Common SDK Exceptions
A **response to an error** that has occurred when processing an SDK or API request.

### Common Exceptions `BatchGetItem`
 - Returns details of **one or more items** from a DynamoDB table.
 - Single Operation, **Limited** to up to 16MB of data and up to 100 items.
 - If DynamoDB cannot return all the items, it returns a **partial result**, along with an exception.
 - Either you requested **more than 100 items, more than 16Mb of data**, that exceeds the throughput og the table

Examples:
 |Exception|Meaning|
 |--|--|
 |`ValidationException`|**Too many items** requested for the `BatchGetItem` call; *Request **fewer** items*|
 |`UnprocessedKeys`|If at least one item is successfully processed, you will receive a message that **some items** were not succesfully processed; *Redeuce the request size*|
 |`ProvisionedThroughputExceededException`|No items were successfully processed, due to exceeding the **provisioned throughput** of the table; *Add capacity (e.g. add DAX)*|

### Common Exceptions `BatchWriteItem`
 - **Puts or deletes** one or more items in one or more DynamoDB tables
 - Single Operation, limited to up to **16Mb** of data and up to **25** put or delete operations.
 - Failed Operations, if any puts or deletes fail, DynamoDB returns a list of **UnprocessedItems**.

Examples:
 |Exception|Meaning|
 |--|--|
 |`UnprocessedItems`|Some of the operations failed. you will need to **retry** the unprocessed items.|
 |`ProvisionedThroughputExceededException`|None of the put or delete operations were processed, due to exceeding the **provisioned throughput** of the table; *Increase the **write capacity** units on the table*|