# ADOP/C Base Networking

The templates here launch the two-tier base networking e.g public, private subnets and NAT gateways.

# Architecture

This template deploys;
* Pre-requisite components.
* VPC.
* Two public subnets in user-provided AZs.
* Creates private subnet.
* Create two NAT gateways (one in each user provided AZ) for the private subnet's route table default route.

# Using this template

## Deployment Instructions

The following provides a high-level overview of the steps required to deploy the base networking.

Note: Take note of the outputs of executed CloudFormation templates as they will be required by some templates as input parameters.

1. In the AWS CloudFormation service launch the `component-pre-eip.yaml'.
2. In the AWS CloudFormation service launch the `component-component-vpc.yaml'.
3. In the AWS CloudFormation service launch the `component-public-network.yaml'.
4. In the AWS CloudFormation service launch the `component-private-network.yaml'.

# CloudFormation

The service uses the AWS CloudFormation service to provision the resource for the base networking.

# User feedback

## Documentation

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
