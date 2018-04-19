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
- Architectures can get extremely complicated, AWS X-ray allows you to debug what is happneing.
- Lambda can do things globally, you can use it to back up S3 buckets to other S3 buckets etc.