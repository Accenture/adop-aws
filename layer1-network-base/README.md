# ADOP/C Base Networking

The templates here launch the two-tier base networking e.g public, private subnets and NAT gateways.

# Architecture

This template deploys;
* Pre-requisite components.
* IAM role for Flow-logs.
* VPC.
* Two public subnets in user-provided AZs.
* One private subnet.
* Two NAT gateways (one in each user provided AZ) for the private subnet's route table default route.

# Using this template

## Deployment Instructions

The following provides a high-level overview of the steps required to deploy the base networking.

Note: Take note of the outputs of executed CloudFormation templates as they will be required by some templates as input parameters.

1. In the AWS CloudFormation service launch the 'component-role-flowLogs.yaml'.
2. In the AWS CloudFormation service launch the `component-pre-eip.yaml'.
2. In the AWS CloudFormation service launch the `component-component-vpc.yaml'.
3. In the AWS CloudFormation service launch the `component-public-network.yaml'.
4. In the AWS CloudFormation service launch the `component-private-network.yaml'.

### Template - Elastic IPs 

#### Outputs

| Output | Description |
|--------|-------------|
| VpnEip | Elastic IP to be assigned to your OpenVPN service (layer4-openvpn/service-openvpn-access-server.yaml)|
| NatGatewayEIP1 | Elastic IP to be assigned to the NAT gateway attached to your first private subnet|
| NatGatewayEIP2 | Elastic IP to be assigned to the NAT gateway attached to your second private subnet|

## Template - IAM Role

#### Outputs

| Output | Description |
|--------|-------------|
| ARNID | ARN ID of the created IAM role for Flow-Logs. |

### Template - VPC

#### Inputs 

| Parameter | Description |
|-----------|-------------|
| VpcCidr | VPC Classless Inter-Domain Routing (CIDR) address range. Choose a range which does not clash with your other VPCs to leave open the possibility of peering. |
| FlowLogARN | The Flow-Log-Role ARN needed to enable Flow Logs. Get the ARN ID from the output of IAM tole created for Flow Logs. |

#### Outputs

| Output | Description |
|--------|-------------|
| BaseVpcId | ID of the outer proxy service security group |
| InternetGatewayId | ID of the outer proxy service security group |


### Template - Public Network

#### Inputs 

| Parameter | Description |
|-----------|-------------|
| BaseVpcId | ID of the VPC where the network will be created (output of component-vpc.yaml) |
| SubnetCidr1 | CIDR Range for public subnet 1. Note - Make sure the range is within the CIDR range of the VPC we have choosen with BaseVpcId and does not clash with eny existing subnets in this VPC. |
| SubnetCidr2 | CIDR Range for public subnet 2. Note - Make sure the range is within the CIDR range of the VPC we have choosen with BaseVpcId and does not clash with eny existing subnets in this VPC.|
| VpcInternetGatewayId | ID of the VPC Internet Gateway (from the output of the VPC CloudFormation)|
| VpcAvailabilityZones | Comma-delimited list of two VPC availability zones in which the subnets are to be created. This will only work in a region with two or more AZs.|
| NATGatewayEIPId1 | EIP Allocation ID for NAT Gateway 1 (from the output of the the EIP CloudFormation)|
| NATGatewayEIPId2 | EIP Allocation ID for NAT Gateway 2 (from the output of the the EIP CloudFormation)|

#### Outputs

| Output | Description |
|--------|-------------|
| NatGatewayAz1ID | ID of the NAT Gateway in AZ 1 |
| NatGatewayAz2ID | ID of the NAT Gateway in AZ 2 |
| PublicSubnetAz1 | ID of the public subnet in AZ 1 |
| PublicSubnetAz2 | ID of the public subnet in AZ 2 |


### Template - Private Network

#### Inputs 

| Parameter | Description |
|-----------|-------------|
| BaseVpcId | VPC ID of where the private subnet will be created. |
| SubnetCidr| IDR Range for the private subnet. Note - Make sure the range is within the CIDR range of the VPC we have choosen with BaseVpcId and does not clash with eny existing subnets in this VPC. |
| NATGatewayId | ID of a NAT Gateway (from the outputs of the public network CloudFormation). |
| AvailabilityZone | Availability zone to launch the subnet. |

#### Outputs

| Output | Description |
|--------|-------------|
| PrivateSubnetId | ID of the private subnet (used for all services launched privately) |


## For Enabling Flow-Logs manually (only for already created VPCs)

1. Use CF template for creating the flow log role. Go to Cloudformation console and click on Create Stack - component-role-flowLogs.yaml
2. After creation, go to Cloudformation console and open the stack we just created and look in the output tab for ARN ID of the created role.
3. Open the Amazon VPC console. In the navigation pane, choose "Your VPC".
4. After selecting your VPC, choose the Flow Logs tab, and then Create Flow Log.
5. In the dialog box, complete the following information:
   	Filter: Select whether the flow log should capture rejected traffic, accepted traffic, or all traffic.
	Role: Specify the name of an IAM role that you created to publish logs to CloudWatch Logs. "<StackName>-Flow-Logs-Role"
	Destination Log Group: Enter the name of a log group in CloudWatch Logs to which the flow logs will be published. "
6. When you are done, click on Create Flow Log.
	NOTE: Name the Destination Log group based on the VPC being used, For eg. For VPC: ADOP-VPC, the Destination Log Group: ADOP-VPC-Flow-Logs.


# CloudFormation

The service uses the AWS CloudFormation service to provision the resource for the base networking.

# User feedback

## Documentation
Documentation will be captured within this README.md.

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
