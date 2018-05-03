# AWS Developer Associate Notes

There are __16 AWS Regions__ as of Feb 2018

## Acronyms
- __ARN__ = Amazone Resource Name
- __SAML__ = Security Assertion Markup Language
- __LDAP__ = Lightweight Directory Access Protocol

## IAM
IAM Key Terms
- __Federation__: combining or joing a list of users in one domain (such as IAM) with alist of other users in another domain (such as Active Directory, Facebook etc)
- __Identity Broker__: a service that allows you to take an identity from point A and join it (federate it) to point B
- __Identity Store__: Services like Active Directory, Facebook, Google etc
- __Identities__: a user of a service like Facebook etc.

### IAM consists of the following;
- Users
- Groups (A way to group our users and apply policies to them collectively)
- Roles
- Policy Document (JSON Doc)

### Other things to note;
- IAM is universal. It does not apply to regions at this time.
- The "root account" is simply the account created when you first setup your AWS account. It has complete Admin access.
- New Users have NO permissions when first created.
- New Users are assigned __Access Key ID & Secret Access Keys__ when first created.
- These are not the same as a password, and you cannot use the Access Key ID & Secret Key to login to the console. You can use this to access AWS via the APIs and Command Line however.
- You only get to view these once. If you lose them, you have to regenerate them. So save them in a secure location.
- Always setup Multifactor Authentication on your root account.
- You can create and customise your own password rotation policies.

### IAM Security Token Service
Steps to remember when setting up/using an Identity Broker
1. Develop an Identity Broker to communicate with LDAP and AWS STS
2. __Identity Broker always authenticates with LDAP first, THEN with AWS STS__
3. Application then get temporary access to AWS resources

Another scenario is to use IAM Roles
1. Develop an Identity Broker to communicate with LDAP and AWS STS
2. __Identity Broker always authenticates with LDAP first, gets an IAM Role associated with a user__
3. Application then authenticates with STS and assumes that IAM Role
4. Application uses that IAM role to interact with S3

### Federated Authentication
1. __Can you authenticate with Active Directory?__ Yes, using SAML (Security Assertion Markup Language)
2. __What is the order of Authentication?__ you ALWAYS authenticate against Active Directory first, then get assigned the temporary security credentials.

### Web Identity Federation Flow
1. Authenticate first with web identity provider
2. Then get your temporary security credentials
3. Call assumeRoleWithIdentity
4. Then asccess AWS resource

## EC2
_note_: Read EC2 FAQ before exam

Linux instances are pay by the second, windows is still pay by the hour.

### EC2 Options
- __On Demand__: allow you to pay a fixed rate by the hour (or by the second) with no commitment.
- __Reserved__: provide you with a capacity reservation, and offer a significant discount on the hourly charge of an instance. 1 Year or 3 Year terms
- __Spot__: enable you to bid whatever price you want for instance capacity, providing for even greater savings if your applications have flexible start and end times.
- __Dedicated Hosts__: Physical EC2 server dedicated for your use. Dedicated Hosts can help you reduce costs by allowing you to use your existing server-bound software licenses.

### On Demand Use Cases:
- Users that want the low cost and flexibility of Amazon EC2 without any up-front payment or long-term commitment
- Applications with short terms, spiky, or unpredictable workloads that cannot be interrupted
- Applications being developed or tested on Amazon EC2 for the first time

### Reserved Use Cases:
- Applications with steady state or predictable usage
- Applications that require reserved capacity
- Users able to make upfront payments to reduce their total computing costs even further
  - Standard RI's (Up to 75% off on demand)
  - Convertible RI's (Up to 54% off on demand) capability to change the attributes of the RI as long as the exchange results in the creation of Reserved Instances of equal or greater value.
  - Scheduled RI's available to launch within the time windows you reserve. This option allows you to match your capacity reservation to a predictable recurring schedule that only requires a fraction of a day, a week, or a month.
  
 ### Spot Use Cases:
 - Applications that have flexible start and end times.
 - Applications that are only feasible at very low compute prices.
 - Users with urgent computing needs for large amounts of additional capacity.
 - If you terminate the instance, you pay for the hour.
 - If AWS termiantes the spot instance, you get the hour it was terminated in for free.

 ### Dedicated Hosts Use Cases:
 - Useful for regulatory requirements that may not support multi-tenant virtualization.
 - Great for licensing which does not support multi-tenancy or cloud deployments.
 - Can be purchased ON-Demand (hourly).
 - Can be purchased as a Reservation for up to 70% off the On-Demand price.

 ## EC2 Instance Types

| Family | Specialty | Use Case |
|--------|-----------|----------|
| D2 | Dense Storage | Fileservers / Data Warehousing / Hadoop |
| R4 | Memory Optimised | Memory Intensive Apps / DBs |
| M4 | General Purpose | Applcation Servers |
| C4 | Compute Optimised | CPU Intensive Apps / DBs |
| G2 | Graphics Intensive | Video Encoding / 3D Application Streaming |
| I2 | High Speed Storage | NoSQL DBs, Data Warehousing etc. |
| F1 | Field Programmable Gate Array | Hardware Acceleration for your code |
| T2 | Lowest Cost, General Purpose | Web Servers, Small DBs |
| P2 | Graphics / General Purpose GPU | Machine Learning, Bitcoin mining etc |
| X1 | Memory Optimised | SAP HANA / Apache Spark etc |

 _How to remember..._

 __DIRTMCGFPX__

 __DR Mc GIFT PX__

 __D__ for Density

 __R__ for RAM

 __M__ main choice for general purpose applications

 __C__ for Compute

 __G__ for Graphics

 __I__ for IOPS

 __F__ for FPGA

 __T__ cheap general purpose (think T2 micro)

 __P__ Graphics (think pics)

 __X__ for Extreme Memory

 ## Elastic Block Store
 EBS Volume Types
 - __General Purpose SSD (GP2)__
  - General purpose, balances both price and performance.
  - Ratio of 3 IOPS per GB with up to 10,000 IOPS and the ability to burst up to 3000 IOPS for extended periods of time for volumes at 3334 GiB and above.
- __Provisioned IOPS SSD (IO1)__
  - Designed for I/O intensive applications such as large relational or NoSQL databases.
  - Use if you need more than 10,000 IOPS.
  - Can provision up to 20,000 IOPS per volume.
- __Throughput Optimised HDD (ST1)__
  - Big data.
  - Data warehouses.
  - Log processing.
  - Cannot be a boot volume.
- __Cold HDD (SC1)__
  - Lowest Cost Storage for infrequently accessed workloads.
  - File server.
  - Cannot be a boot volume.
- __Magnetic (Standard)__
  - Lowest cost per gigabyte of all EBS volume types that is bootable. Magnetic volumes are ideal for workloads where data is accessed infrequently, and applications where the lowest storage cost is important.
  
__You cannot mount 1 EBS volume to multiple EC2 instances, instead use EFS.__

1 Subnet === 1 Availability zones.

- Termination Protection is turned off by default, you must turn it on.
- On a EBS-backed instance, the default action is for the root EBS volume to be deleted when the instance is terminated.
- EBS Root Volumes of your DEFAULT AMI's cannot be encrypted. You can also use a third party tool (such as bit locker etc) to encrypt the root volume, or this can be done when creating AMI's in the AWS console or using the API.

### Volumes & Snapshots
- Volumes exist on EBS:
  - Virtual Hard Disk
- Snapshots exist on S3
- Snapshots are point in time copies of Volumes.
- Snapshots are incremental - this means that only the blocks that have changed since your last snapshot are moved to S3.
- If this is your first snapshot, it may take some time to create.

### Snapshots of Root Device Volumes
- To create a snapshot for Amazon EBS volumes that serve as root devices, you should stop the instance before starting the snapshot.
- However, you can take a snap while the instance is running.
- You can create AMI's from both Volumes and Snapshots.
- You can change EBS volume sizes on the fly, including changing the size and storage type.
- Volumes will ALWAYS be in the same availability zone as the EC2 instance.
- To movean EC2 volume from one AZ/Region to another, take a snap or an image of it, then copy it to the new AZ/Region.

### Volumes vs Snapshots
- Snapshots of encrypted volumes are encrypted automatically.
- Volumes restored from encrypted snapshots are encrypted automatically.
- You can share snapshots, but only if they are unencrypted.
  - These snapshots can be shared with othe AWS accounts or made public.
  
### Security Groups
A security group is a virtual firewall.

- All Inbound Traffic is blocked by default.
- All Outbound Traffic is allowed.
- Changes to Security Groups take effect immediately.
- You can have any number of EC2 instances within a Security Group.
- You can have multiple Security Groups attached to EC2 instances.
- Security Groups are __STATEFUL__.
  - If you create an inbound rule allowing traffic in, that traffic is automatically allowed back out again.
- You cannot block specific IP addresses using Security Groups, instead use Network Access Control Lists.
- You can specify allow rules, but not deny rules.

## EFS - Elastic File System
Block based storage.

Perfect for File Server.

Allows multiple EC2 instances to mount it.
- Supports the Network File System version 4 (NFSv4) protocol.
- You pay only for the storage you use (no pre-provisioning required).
- Can scale up to the petabytes.
- Can support thousands of concurrent NFS connections.
- Data is stored across multiple AZ's within a region.
- Read After Write Consistency.

### EC2 CLI Exam Tips
Reference: https://docs.aws.amazon.com/cli/latest/reference/ec2/index.html
- Use:
	- AWS EC2 DESCRIBE_INSTANCES
	- AWS EC2 DESCRIBE_IMAGES
	- AWS EC2 RUN_INSTANCES
	- AWS EC2 TERMINATE-INSTANCES

- Do not confuse START-INSTANCES with RUN-INSTANCES

## Lambda

### What is Lambda
AWS Lambda is a compute service where you can upload your code and create a Lambda function. AWS Lambda takes care of provisioning and managing the servers that you use to run the code. You don't have to worry about operating systems, patching, scaling, etc. You can use Lambda in the following ways.

- As an event-driven compute service where AWS Lambda runs your code in response to events. These events could be changes to data in an Amazon S3 bucket or an Amazon DynamoDB table.
- As a compute service to run your code in response to HTTP requests using Amazon API Gateway or API calls made using AWS SDKs.

### Lambda Triggers
- API Gateway
- AWS IoT
- Alexa Skills Kit
- Alexa Smart Home
- CloudFront
- CloudWatch Events
- CloudWatch Logs
- CodeCommit
- Cognito Sync Trigger
- DynamoDB
- Kinesis
- S3
- SNS

### Runtimes / Languages Support by Lambda
- C# (.NET Core 1.0)
- C# (.NET Core 2.0)
- Go 1.x
- Java 8
- Node.js 4.3
- Node.js 6.10
- Node.js 8.10
- Python 2.7
- Python 3.6

### How is Lambda Priced?

- Number of requests
	- First 1 millions requests are free. $0.20 per 1 million requests thereafter.
- Duration
	- Duration is calculated from the time your code begins executing until it returns or otherwise terminates, rounded up to the nearest 100ms. The price depends on the amount of memory you allocate to your function. You are charged $0.00001667 for every GB-second used.


- Lambda scales out (not up) automatically
- Lambda functions are independant, 1 event = 1 function
- Lambda is serverless
- Maximum execution time is 5 minutes
- Lambda functions can trigger other Lambda functions, 1 event can = x functions if functions trigger other functions
- Architectures can get extremely complicated, AWS X-ray allows you to debug what is happening.
- Lambda can do things globally, you can use it to back up S3 buckets to other S3 buckets etc.

### Instance Metadata and User Data
Documentation: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html
To list all meta-data options: http://169.254.169.254/latest/meta-data

### Elastic Load Balancers
- Instances monitored by ELB are reported as; InService, or OutOfService
- Health Checks check the instance health by talking to it.
- Have their own DNS name. You are never given an IP address.
- Read the ELB FAQ for Classic Load Balancers.

### Know the available SDKs
http://aws.amazon.com/tools/
- Android, iOS, Javascript (Browser)
- Java
- .Net
- Node.js
- PHP
- Python
- Ruby
- Go
- C++

### SDK Default Regions
- Default Region - US-EAST-1

- Some have default regions (Java)
- Some do not (Node.js)

### EC2 Exam Tips
- Know the difference between;
  - On Demand
  - Spot
  - Reserved
  - Dedicated Hosts
- Remember with spot instances;
  - If you terminate the instance, you pay for the hour.
  - If AWS terminates the spot instance, you get the hour it was terminated in for free.

### EBS Exam Tips
- EBS Consists of;
  - SSD, General Purpose - GP2 - (Up to 10,000 IOPS).
  - SSD, Provisioned IOPS - IO1 - (More than 10,000 IOPS).
  - HDD, Throughput Optimised - ST1 - frequently accessed workloads.
  - HDD, Cold - SC1 - less frequently accessed data.
  - HDD, Magnetic - Standard - cheap, infrequently accessed storage.
- You cannot mount 1 EBS volume to multiple EC2 instances, instead use EFS.

### EC2 Lab Exam Tips
- Termination Protection is turned off by default, you must turn it on.
- On a EBS-backed instance, the default action is for the root EBS volume to be deleted when the instance is terminated.
- Root Volumes cannot be encrypted by default, you need a third party tool (such as bit locker etc) to encrypt the root volume.
- Additional volumes can be encrypted.

### Volumes vs Snapshots
- Volumes exist on EBS
  - Virtual Hard Disk
- Snapshots exist on S3
- You can take a snapshot of a volume, this will store that volume on S3.
- Snapshots are point in time copies of Volumes.
- Snapshots are incremental, this means that only the blocks that have changed since your last snapshot are moved to S3.
- If this is your first snapshot it's going to take some time to create.

### Volumes vs Snapshots - Security
- Snapshots of encrypted volumes are encrypted automatically.
- Volumes restored from encrypted snapshots are encrypted automatically.
- You can share snapshots, but only if they are unencrypted.
  - These snapshots can be shared with other AWS accounts or made public.

### Snapshots of Root Device Volumes
- To create a snapshot for Amazon EBS volumes that serve as root devices, you should stop the instance before taking the snapshot.

### EBS vs Instance Store - Exam Tips
- Instance Store Volumes are sometimes called Ephemeral Storage.
- Instance store volumes cannot be stopped. If the underlying host fails, you will lose your data.
- EBS backed instances can be stopped. You will not lose the data on this instance if it is stopped.
- You can reboot both, you will not lose your data.
- By default, both ROOT volumes will be deleted on termination, however with EBS volumes, you can tell AWS to keep the root device volume.

### How can I take a Snapshot of a RAID Array?
- Problem - Take a snapshot, the snapshot excludes data held in the cache by applications and the OS. This tends not to matter on a single volume, however using multiple volumes in a RAID array, this can be a problem due to interdependencies of the array.
- Solution - Take an application consistent snapshot.
  - Stop the application from writing to disk.
  - Flush all caches to the disk.
  
  - How can do this?
    - Freeze the file system.
    - Unmount the RAID Array.
    - Shutting down the associated EC2 instance.

### Amazon Machine Images - Exam Tip
- AMI's are regional. You can only launch an AMI from the region in which it is stored. However you can copy AMI's to other regions using the console, command line or the Amazon EC2 API.

### Cloudwatch
- Standard Monitoring = 5 Minutes.
- Detailed Monitoring = 1 Minute.

- CloudWatch is for performance monitoring.
- CloudTrail is for auditing.

### What can I do with CloudWatch?
- Dashboards - Creates awesome dashboards to see what is happening with your AWS environment.
- Alarms - Allows you to set Alarms that notify you when particular thresholds are hit.
- Events - CloudWatch Events helps you to respond to state changes in your AWS resources.
- Logs - CloudWatch Logs help you to aggregate, monitor, and store logs.

### IAM Roles Exam Tips
- Roles more secure than storing your access key and secret access key on individual EC2 instances.
- Roles are easier to manage.
- Roles can be assigned to an EC2 instance AFTER it has been provisioned using both the command line and the AWS console.
- Roles are universal, you can use them in any region.

### Instance Meta-data
- Used to get information about an instance (such as public ip).
- curl http://169.254.169.254/latest/meta-data/

### EFS Features
- Supports the Network File System version 4 (NFSv4) protocol.
- You only pay for the storage you use (no pre-provisioning required).
- Can scale up to the petabytes.
- Can support thousands of concurrent NFS connections.
- Data is stored across multiple AZ's within a region.
- Read After Write Consistency.

### What is Lambda?
AWS Lambda is a compute service where you can upload your code and create a Lambda function. AWS Lambda takes care of provisioning and managing the servers that you use to run the code. You don't have to worry about operating systems, patching, scaling, etc. You can use Lambda in the following ways.
- As an event-driven compute service where AWS Lambda runs your code in response to events. These events could be changes to data in an Amazon S3 bucket or an Amazon DynamoDB table.
- As a compute service to run your code in response to HTTP requests using Amazon API Gateway or API calls made using AWS SDKs. 

## S3
### The Basics
- S3 is Object based ie. allows you to upload files.
- Files can be from 0 Bytes to 5TB.
- There is unlimited storage.
- Files are stored in Buckets.
- S3 is a universal namespace, that is, names must be unique globally.
- https://s3-eu-west-1.amazonaws.com/<bucketname>
- When you upload a file to S3 you will receive a HHTP 200 code if the upload was successful.

### Data Consistency Model for S3
- Read after Write consistency for PUTS of new Objects.
- Eventual Consistency for overwrite PUTS and DELETES (can take sometime to propagate).

### S3 - S3 is a simple key, value store
- S3 is Object based. Objects consist of the following;
  - Key (This is simply the name of the object)
  - Value (This is simply the data and is made up of a sequence of bytes).
  - Version ID (Important for versioning).
  - Metadata (data about the data you are storing).
  - Subresources
    - Access Control Lists
    - Torrent
  - Built for 99.99% availability for the S3 platform.
  - Amazon Guarantee 99.9% availability.
  - Amazon guarantees 99.999999999% durability for S3 informations (Remember 11 x 9's).
  - Tiered Storage available.
  - Lifecycle Management.
  - Versioning.
  - Encryption.
  - Secure you data using Access Control Lists and Bucket Policies.

### S3 - Storage Tiers/Classes
- S3 - 99.99% availability, 99.999999999% durability, stored redundantly across multiple devices in multiple facilities and is designed to sustain the loss of 2 facilities concurrently.
- S3 - IA (infrequently Accessed) For data that is accessed less frequently, but requires rapid access when needed. Lower fee than S3, but you are charged a retrieval fee.
- Reduced Redundancy Storage - Designed to provide 99.99% durability and 99.99% availability of objects over a given year.
- Glacier - Very cheap, but used for archival only. It takes 3 - 5 hours to restore from Glacier.

Glacier is an extremely low-cost storage service for data archival. Amazon Glacier stores data for as little as $0.01 per gigabyte per month, and is optimized for data that is infrequently accessed and for which retrieval times of 3 to 5 hours are suitable.

### S3 - Charges
- Charged for;
  - Storage
  - Requests
  - Storage Management Pricing
  - Data Transfer Pricing
  - Transfer Acceleration

Amazon S3 Transfer Acceleration enables fast, east, and secure transfers of files over long distances between your end users and an S3 bucket. Transfer Acceleration takes advantage of Amazon CloudFront's globally distributed edge locations. As the data arrives at an edge location, data is routed to Amazon S3 over an optimized network path.

### S3 - Exam Tips
- Remember that S3 is Object based ie. allows you to upload files.
- Files can be from 0 Bytes to 5 TB.
- There is unlimited storage.
- Files are stored in Buckets.
- S3 is a universal namespace, that is, names must be unique globally.
- http://<region>.amazonaws.com/<bucketname>
- Read after Write consistency for PUTS of new Objects.
- Eventual Consistency for overwrite PUTS and DELETES (can take some time to propagate).
- S3 Storage Classes/Tiers
  - S3 (durable, immediately available, frequently accessed).
  - S3 - IA (durable, immediately available, infrequently accessed).
  - S3 - Reduced Redundancy Storage (data that is easily reproducible, such as thumb nails etc).
  -  Glacier - Archived data, where you can wait 3 - 5 hours before accessing.
  -  Remember the core fundamentals of an S3 object;
    -  Key (name)
    -  Value (data)
    -  Version ID
    -  Metadata
    -  Subresources
      -  ACL
      -  Torrent
    - Object based storage only (for files).
    - Not suitable to install an operating system on.
    - Successful uploads will generate a HTTP 200 status code.
    - Read the S3 FAQ before taking the exam. It comes up A LOT!

### Create an S3 Bucket - Exam Tips
- Buckets are a universal name space.
- Upload an object to S3 receive a HTTP 200 Code.
- S3, S3 IA, S3 Reduced Redundancy Storage.
- Encryption.
  - Client Side Encryption.
  - Server Side Encryption.
	- Server side encryption with Amazon S3 Managed Keys (SSE-S3).
	- Server side encryption with KMS (SSE-KMS).
	- Server side encryption with Customer Provided Keys (SSE-C).
- Control access to buckets using either a bucket ACL or using Bucket Policies.
- BY DEFAULT BUCKETS ARE PRIVATE AND ALL OBJECTS STORED INSIDE THEM ARE PRIVATE. 

### S3 Static Website Hosting
URL Format: http://<bucketname>.s3-website-<region>.amazonaws.com

### S3 - Versioning Exam Tips
- Stores all versions of an object (including all writes and even if you delete an object).
- Great backup tool.
- Once enabled, Versioning cannot be disabled, only suspended.
- Integrates with Lifecycle rules.
- Versioning's MFA Delete capability, which uses multi-factor authentication, can be used to provide an additional layer of security.

### S3 - Cross Region Replication Exam Tips
- Versioning must be enabled on both the source and destination buckets.
- Regions must be unique.
- Files in an existing bucket are not replicated automatically. All subsequent updated fies will be replicated automatically.
- You cannot replicate to multiple buckets or use daisy chaining (at this time).
- Delete markers are replicated.
- Deleting individual versions or delete markers will not be replicated.
- Understand what Cross Region Replication is at a high level.

### S3 - Lifecycle Management Exam Tips
- Can be used in conjunction with versioning.
- Can be applied to current versions and previous versions.
- Following actions can now be done:
  - Transition to the Standard - Infrequent Access Storage Class (128k and 30 days after the creation date).
  - Archive to the Glacier Storage Class (30 days after IA, if relevant).
  - Permenently Delete.

## Cloudfront

A content delivery network (CDN) is a system of distributed servers (network) that deliver webpages and other web content to a user based on the geographic locations of the user, the origin of the webpage and a content delivery server.

### Key Terminology
- Edge Location - This is the location where the content will be cached. This is seperate to an AWS Region/AZ
- Origin - This is the origin of all the files that the CDN will distribute. This can be either an S3 Bucket, an EC2 Instance, an Elastic Load Balancer or Route53.
- Distribution - This is the name given to the CDN which consists of a collwction of Edge Locations.
- Web Distribution - Typically used for Websites.
- RTMP - Used for Media Streaming.

Amazon CloudFront can be used to deliver your entire website, including dynamic, static, streaming, and interactive content using a global nework of edge locations. Requests for your content are automatically routed to the nearest edge location, so content is delivered with the bedst possible performance.

Amazon CloudFront is optimised to work with other Amazon Web Services, like Amazon Simple Storage Service (Amazon S3), Amazon Elastic Compute Cloud (Amazon EC2), Amazon Elastic Load Balancing, and Amazon Route53. Amazon CloudFront also works seamlessly with any non-AWS origin server, which stores the original, definitive versions of your files.

### CloudFront - Exam Tips
- Edge Location - This is the location where content will be cached. This is seperate to an AWS Region/AZ
- Origin - This is the origin of all the files that the CDN will distribute. This can be either an S3 Bucket, an EC2 Instance, an Elastic Load Balancer or Route53.
- Distribution - This is the name given the CDN which consists of a collection of Edge Locations.
  - Web Distribution - Typically used for Websites.
  - RTMP - Used for Media Streaming.
- Edge Locations are not just for READ only, you can write top them too. (ie. put an object on to them).
- Objects are cached for the life of the TTL (Time To Live).
- You can clear cached objects, but you will be charged.

## S3 - Security & Encryption

### Securing your buckets
- By default, all newly created buckets are PRIVATE.
- You can setup access control to your buckets using;
  - Bucket Policies.
  - Access Control Lists.
- S3 buckets can be configured to create access logs which log all requests made to the S3 bucket. This can be done to another bucket.

### Encryption
- In Transit;
  - SSL/TLS
- At Rest
  - Server Side Encryption
    - S3 Managed Keys - **SSE-S3**
    - AWS Key Management Service, Managed Keys - **SSE-KMS**
    - Server Side Encryption With Customer Provided Keys - **SSE-C**
  - Client Side Encryption
  
## Storage Gateway

### How Can I Backup My Data?
- Write backup data to S3 directly, using API calls.
- Write backup data to Storage Gateway, which then securely replicates it to S3.

### Storage Gateway 2017
- File Interface
- Volume Interface
  - Gateway-Cached Volumes
  - Gateway-Stored Volumes
- Tape Interface
  - Gateway-Virtual Tape Library

- File Volumes
  - NFS
- Volume Gateway
  - Cached (Gateway-Cached Volumes)
    - -iSCSI based block storage
  - Stored (Gateway-Stored Volumes)
    - -iSCSI based block storage
- Tape Gateway (Gateway-Virtual Tape Library)
  - -iSCSI based virtual tape solution

### File Gateway
File gateway provides a virtual file server, which enables you to store and retrieve AMazon S3 objects through standard file storage protocols. File gateway allows your existing file-based applications or devices to use secure and durable cloud storage without needing to be modified. With file gateway, your configured S3 buckets will be available as Network File System (NFS) mount points. Your applications read and write files and directories over NFS, interfacing to the gateway as a file server.

In turn, the gateway translates these file operations into object requests on your S3 buckets. Your most recently used data is cached on the gateway for low-latency access, and data transfers between your data center and AWS is fully managed and optimised by the gateway.

### Gateway-Cached Volumes
You can store your primary data in Amazon S3, and retain your frequently accessed dta locally. Gateway-cached volumes provide substantial cost savings on primary storage, minimise the need to scale your storage on-premises, and retain low-latency access to your frequently accessed data.

### Gateway-Stored Volumes
In the event you need low-latency access to your entire data set, you can configure your on-premises data gateway to store your primary data locally, and asynchronously back up point-in-time sapshots of the data to Amazon S3.

### Gateway-Virtual Tape Library
You can have a limitless collectin of virtual tapes. Each virtual tape can be stored in a Virtual Tape Library backed by Amazon S3 or a Virtual Tape Shelf (VTS) backed by Amazon Glacier.

### Storage Limits
- File Gateway
  - Unlimited amount of storage. However maximum file size is 5TB.
- Gateway-Cached Volumes
  - Each Volume can store up to 32TB in Size.
  - 32 Volumes supported. 1PB of data can be stored (32 x 32).
- Gateway-Stored Volumes
  - Each Volume can store up to 16TB in Size.
  - 32 Volumes supported. 512TB of data can be stored (32 x 16).
- Gateway-Vrtual Tape Library
  - Virtual Tape Library (S3) 1500 virtual tapes (1PB).
  - Virtual Tape Shelf (Glacier) unlimited tapes.

### General Facts
- Can be deployed on-premise, or as an EC2 instance.
- Can schedule snapshots.
- You can use Storage Gateway with Direct Connect.
- You can implement bandwidth throttling.
- On-premise need either Vmware's ESXi or Hyper-V.

### Storage Requirements
- For gateway-cached volume configuration, you will need storage for the local cache and an upload buffer.
- For gateway-stored volume configuration, you will need storage for your entire dataset and an upload buffer. Gateway-stored volumes can range from 1 GiB to TB. Each gateway configured for gateway-stored volumes can support up to 12 volumes and a total volume storage of 16TB.
- For gateway-VTL configuration, you will need storage for the local cache and an upload buffer.

### Networking Requirements
- Open port 443 on your firewalls.
- Internally, you will need to allow port 80 (activation only), port 3260 (by local systems to connect to iSCSI targets exposed by the gateway) and port UDP 53 (DNS).

### Encryption
- Data in transit is secured using SSL.

### Gateway-Cached and Gateway-Stored Volumes
- You can take point-in-time, incremental snapshots of your volume and store them in Amazon S3 in the form of Amazon EBS snapshots.
- Snapshots can be initiated on a scheduled or ad-hoc basis.

- Gateway Stored Snapshots
  - If your volume data is stored on-premises, snapshots provide durable, off-site backups in Amazon S3.
  - You can create a new Gateway-Stored volume from a snapshot in the event you need to recover a backup.
  - You can also use a snapshot of your Gateway-Stored volume as the starting point for a new Amazon EBS volume which you can then attach to an Amazon EC2 instance.

- Gateway Cached Snapshots
  - Snapshots can be used to preserve versions of your data, allowing you to revert to a prior version when required or to repurpose a point-in-tie version as a new Gateway-Cached volume.

### Gateway-Virtual Tape Library Retrieval
The virtual tape containing your data must be stored in a Virtual Tape Library before it can be accessed. Access to virtual tapes in your Virtual Tape Library is instantaneous.

If the virtual tape containing your data is in your Virtual Tape Shelf, you must first retrieve the virtual tape from your Virtual Tape Shelf. It takes about 24 hours for the retrieved virtual tape to be available in the selected Virtual Tape Library.

### Gateway-Virtual Tape Library Supports
- Symantec NetBackup version 7.x
- Symantec Backup Exec 2012
- Symantec Backup Exec 2014
- Symantec Backup Exec 15
- Microsoft System Center 2012 Rs Data Protection Manager
- Veeam Backup & Replication V7
- Veeam Backup & Replication V8
- Dell NetVault Backup 10.0

### Exam Tips
- Know the four different Storage Gateway Types:
  - File Gateway
  - Volume Gateway
    - Cached - OLD NAME (Gateway-Cached Volumes)
    - Stored - OLD NAME (Gateway-Stored Volumes)
  - Tape Gateway - OLD NAME (Gateway-Virtual Tape Library)
  - Remember that access to virtual tapes in your virtual library are instantaneous. If your tape is in the virtual tape shelf (glacier) it can take 24 hours to get back to your virtual tape library.
  - Encrypted using SSL for transit and is encrypted at rest in Amazon S3 using AES-256.
  - Gateway-Stored Volumes - stores data as Amazon EBS Snapshots in S3.
  - Snapshots can be scheduled.
  - Bandwidth can be throttled (good for remote sites).
  - You need a storage gateway in each site if using multiple locations.

## Snowball

### Before Snowball - Import/Export Disk
AWS Import/Export Disk accelerates moving large amounts of data into and out of the AWS cloud using portable storage devices for transport. AWS Import/Export Disk transfers your data directly onto and off of storage devices using Amazon's high-speed internal network and bypassing the Internet.

### Types of Snowballs
- Snowball
- Snowball Edge
- Snowmobile

### Snowball
Snowball is a petabyte-scale data transport solution that uses secure appliances to transfer large amounts of data into and out of AWS. Using Snowball addresses common challenges with large-scale data transfers including high network costs, long transfer times, and security concerns. Transferring data with Snowball is simple, fast, secure, and can be as little as one-fifth the cost of high-speed internet.

80TB snowball in all regions, Snowball use multiple layers of security designed to protect your data including tamper-resistant enclosures, 256-bit encryption, and an industry Trusted Platform Module (TPM) designed to ensure both security and full chain-of-custody of your data. Once the data transfer has been processed and verified, AWS performs a software erasure of the Snowball appliance.
