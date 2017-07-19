# ADOP Outer Proxy Service

The templates in here implement the outer proxy of the two tier network architecture.

# Using this template 

## Pre-requisites

### Base Networking

The base networking infrastructure deployed. 

### Domain Creation & SSL Certificates

Please setup a domain for the outer proxy service and self-signed SSL certificates (if you have not yet acquired certs from an approved certificate provider). As an example, you can refer to [AWS Route53](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/registrar.html) to learn how to setup a domain there. If you have not yet acquired a domain, you can still proceed with just self-signed certificates but they will have to be uploaded to AWS IAM, as opposed to AWS Certificate Manager.

Upload your certificates to the AWS certificate manager service in your region.
1. Click "import certificate"
2. Copy the x509 SSL certificate into the "Certificate body" text field.
3. Copy the certificate private RSA key into the "Certificate private key" text field.
4. Take note of the Amazon Resource Name (ARN) for the uploaded certificate as this is required to assign the x509 certificates to the Elastic Load Balancer (ELB) for the public reverse proxy.

#### For assistance with generating self-signed certificates and uploading them to AWS IAM (as an alternative to Certificate Manager), you can refer to the [auxiliary scripts here](https://github.com/Accenture/adop-docker-compose/tree/master/provision/aws/ssl)

## Deployment Instructions

The following provides a high-level overview of the steps required to deploy the outer proxy service.

1. In the AWS CloudFormation service launch the `public-proxy-s3-bucket.yaml` template.
2. From this repository folder copy `config/proxy/sites-enabled/base.conf` into the created S3 Bucket and apply an [AWS authenticated user read only policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-iam-policies.html) to the files. Take note of the S3 Bucket name and path to your base.conf file.
3. In the AWS CloudFormation service launch the `public-proxy-security-group.yaml` template. Take note of the proxy security group name.
4. In the AWS CloudFormation service launch the `public-proxy.yaml` template. Take note of the AWS ELB domain name.

### Template - Public Proxy S3 Bucket 

#### Inputs

| Parameter | Description |
|-----------|-------------|
| S3BucketName | S3 Bucket name to store proxy configuration |

#### Outputs

| Output | Description |
|--------|-------------|
| S3BucketName | S3 Bucket name where proxy configuration is stored|

### Template - Public Proxy Security Group

#### Inputs 

| Parameter | Description |
|-----------|-------------|
| BaseVpcId | VPC ID of where the security group will be created |
| ProxyWhiteListCidr| The Cidr range of IPs to whitelist. By default the Proxy is open to the world.|

#### Outputs

| Output | Description |
|--------|-------------|
| PublicProxySG | ID of the outer proxy service security group |


### Template - Public Proxy

#### Inputs 

| Parameter | Description |
|-----------|-------------|
| BaseVpcId | VPC ID of where the security group will be created |
| PublicSubnets| Comma-delimited list of public subnets to deploy the proxy |
| VpcAvailabilityZones | Comma-delimited list of Two Availability zones of the public subnets in the VPC. |
| SSLCertificateARN | ARN of the SSL certificate to use for the ELB. Note: ELB Offloads SSL for the outer proxy. |
| InternalProxyDnsName | The internal DNS name/IP address (your ADOP/C private IP) |
| OuterProxyClusterSize | Desired number of nodes in the outer proxy ASG. |
| OuterProxyKeyPairName | Outer proxy keypair name. |
| ProxyInstanceType| Proxy instance type. |
| PublicProxySG | ID of public facing proxy SG |
| ProxyConfigS3BucketName | Proxy Configuration S3 Bucket Name. |
| ProxyConfigS3Url | S3 URL of your base.conf file in the format s3://<BUCKET_NAME>/<DIRECTORY_PATH>/base.conf |
| NtpRegion | The geographical NTP region where your NTP server is located (can be a continent or a country). This will appear in your /etc/ntp.conf file such as "server [1:4].<NtpRegion>.pool.ntp.org". Please refer here http://www.pool.ntp.org/en/ |
| NATGatewayIdEipIP1 | NAT Gateway Elastic IP IPv4 Address and /32 Cidr 1 e.g. 54.1.1.1/32 (output of the pre-requisite EIP CloudFormation) |
| NATGatewayIdEipIP2 | NAT Gateway Elastic IP IPv4 Address and /32 Cidr 2 e.g. 54.1.1.1/32 (output of the pre-requisite EIP CloudFormation) |

#### Outputs

| Output | Description |
|--------|-------------|
| ElbAddress | Elastic Load Balancer Address |

#### Note: This template creates EBS volumes which are set to not terminate on deletion of this template. You must manually go into AWS -> EC2 -> Volumes and delete orphaned volumes.

# CloudFormation

The service uses the AWS CloudFormation service to provision the resource required for the Outer proxy service.

# User feedback

## Documentation

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
