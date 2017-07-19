# ADOP/C Hardened Two-Tier Network Architecture
This repository contains a hardened, 2-tiered implementation of the [DevOps Platform](https://github.com/Accenture/adop-docker-compose)

The CloudFormation templates in this repository implement the following network architecture:


# Architecture

The architecture has been decomposed into the following fine-grained layers.
 * Layer 1 - Base networking (VPC, Subnets)
 * Layer 2 - Private Services (ADOP/C)
 * Layer 3 - Public Services (Outer Proxy, OpenVPN)


## Pre

Pre-requisite CloudFormation scripts for;
  * Allocating EIPs for VPN, NAT Gateway instance one and two.

## Layer 1

Defines the base networking:
  * VPC
  * Public networking
  * Private networking

## Layer 2

Provisions your ADOP/C instance in a private subnet.

## Layer 3

Defines outer proxy and OpenVPN service that support the infrastructure.
