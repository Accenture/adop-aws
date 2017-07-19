# ADOP/C Service

The template here launches an ADOP/C in a private subnet, with all Docker data externalised on EBS volumes.

# Using this template 

## Deployment Instructions

The following provides a high-level overview of the steps required to deploy the ADOP/C service.

In the AWS CloudFormation service launch the `service-adop-c.json'.

### Inputs

| Parameter | Description |
|-----------|-------------|
| AdopUsername | ADOP admin account username. Username can not be set to 'admin' or 'Admin' and it must contain only letters and numbers. Minimum length is 3. |
| AdopUserPassword | ADOP admin account password. Must be at least 8 characters long and contain at least one number, and cannot contain word "password" or your username |
| SecretS3BucketStore | Name of an S3 bucket where your platform secrets file will be kept temporarily so that it can be obtained from outside the host. NOTE - Delete this file once you have obtained a copy of it. Read [here](http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) to learn about creating an S3 bucket. |
| VpcID | VPC ID where your instance will be created | 
| VPCCidr | VPC Cidr range to allow connection to the instance from within the VPC. | 
| VPCSubnet | Private subnet ID of your stack (ensure a NAT gateway is attached) |
| KeyName | Key-pair name to use. Note : Make sure you choose a key which you have. |
| NtpRegion | The geographical NTP region where your NTP server is located (can be a continent or a country). This will appear in your /etc/ntp.conf file such as "server [1:4].<NtpRegion>.pool.ntp.org". Please refer here http://www.pool.ntp.org/en/ |

#### Note: This template creates EBS volumes which are set to not terminate on deletion of this template. You must manually go into AWS -> EC2 -> Volumes and delete orphaned volumes.

# CloudFormation

The service uses the AWS CloudFormation service to provision the resource required to provision an ADOP/C.

# User feedback

## Documentation
Documentation will be captured within this README.md.

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
