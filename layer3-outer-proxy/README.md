# ADOP Outer Proxy Service

The templates in here implement the outer proxy of the two tier network architecture.

# Using this template 

## Pre-requisites

### Base Networking

The base networking infrastructure deployed. 

### Domain Creation & SSL Certificates

Please setup a domain for the outer proxy service and self-signed SSL certificates (if you have not yet acquired certs from an approved certificate provider)

Upload your certificates to the AWS certificate manager service in your region.
1. Click "import certificate"
2. Copy the x509 SSL certificate into the "Certificate body" text field.
3. Copy the certificate private RSA key into the "Certificate private key" text field.
4. Take note of the Amazon Resource Name (ARN) for the uploaded certificate as this is required to assign the x509 certificates to the Elastic Load Balancer (ELB) for the public reverse proxy.

## Deployment Instructions

The following provides a high-level overview of the steps required to deploy the outer proxy service.

1. In the AWS CloudFormation service launch the `public-proxy-s3-bucket.yaml` template.
2. From this repository folder copy `config/proxy/sites-enabled/` into the created S3 Bucket into a 'conf/sites-enabled' folder and apply an AWS authenticated user read only policy to the files.
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
| VpnSecurityGroup | Vpn Security Group for administrating EC2 instances|
| SSLCertificateARN | ARN of the SSL certificate to use for the ELB. Note: ELB Offloads SSL for the outer proxy. |
| OuterProxyClusterSize | Desired number of nodes in the outer proxy ASG. |
| OuterProxyKeyPairName | Outer proxy keypair name. |
| PublicProxySG | ID of public facing proxy SG |
| ProxyInstanceType| Proxy instance type. |
| InternalProxyDnsName | The internal proxy DNS name/address (your ADOP/C proxy private IP) |
| NATGatewayIdEipIP1 | NAT Gateway Elastic IP IPv4 Address and Cidr 1 e.g. 54.1.1.1/32 (output of the public network CloudFormation) |
| NATGatewayIdEipIP2 | NAT Gateway Elastic IP IPv4 Address and Cidr 2 e.g. 54.2.3.4/32 (output of the public network CloudFormation)
| VpcAvailabilityZones | Comma-delimited list of Two Availability zones of the public subnets in the VPC. |
| ProxyConfigS3BucketName | Proxy Configuration S3 Bucket Name. |
| ProxyConfigS3BucketPath |  Proxy configuration S3 Bucket path. Default "conf" |
| ProxyConfigS3BucketRegion | S3 Bucket region. |

#### Outputs

| Output | Description |
|--------|-------------|
| ElbAddress | Elastic Load Balancer Address |

# CloudFormation

The service uses the AWS CloudFormation service to provision the resource required for the Outer proxy service.

# User feedback

## Documentation

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
