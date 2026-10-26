# AWS VPC Peering Project

**Project:** EWB AWS VPC Peering  
**Batch:** Batch 1  
**AWS Region:** Asia Pacific (Mumbai) — `ap-south-1`  
**Completion date:** 26 October 2026

## Project Overview

This project demonstrates private connectivity between two Amazon Virtual Private Clouds (VPCs) using VPC Peering. The lab included a public subnet with a bastion host in VPC A and private application instances in VPC A and VPC B. Connectivity was tested using ICMP ping in both directions, SSH access to the private App-B instance through the bastion host, and a controlled ICMP failure-and-recovery test.

## Architecture and Network Plan

| Resource | Name | CIDR / Private IP |
|---|---|---|
| VPC A | `ewb-vpc-a` | `10.10.0.0/16` |
| VPC B | `ewb-vpc-b` | `10.20.0.0/16` |
| Public subnet A | `ewb-a-public` | `10.10.1.0/24` |
| Private subnet A | `ewb-a-private` | `10.10.2.0/24` |
| Private subnet B | `ewb-b-private` | `10.20.1.0/24` |
| Bastion host | `ewb-bastion` | Public IP: `13.207.40.124`; private IP not recorded |
| App-A | `ewb-app-a` | `10.10.2.10` |
| App-B | `ewb-app-b` | `10.20.1.10` |

See the architecture diagram in the `architecture/` folder.

## AWS Services and Configuration

- Amazon VPC and subnets
- VPC Peering
- Amazon EC2
- Internet Gateway
- Route tables
- Security groups
- SSH and ICMP connectivity tests

### Route Table Design

| Route table | Subnet association | Key routes configured during the lab |
|---|---|---|
| `ewb-rt-a-public` | `ewb-a-public` (`10.10.1.0/24`) | Local route; `0.0.0.0/0` to the Internet Gateway; `10.20.0.0/16` to VPC Peering |
| `ewb-rt-a-private` | `ewb-a-private` (`10.10.2.0/24`) | Local route; `10.20.0.0/16` to VPC Peering |
| `ewb-rt-b-private` | `ewb-b-private` (`10.20.1.0/24`) | Local route; `10.10.0.0/16` to VPC Peering |

### Security Group Summary

- **Bastion:** SSH (TCP 22) allowed from the configured client public IP using a `/32` source restriction.
- **App-A:** Recorded inbound rules allowed SSH (TCP 22) from `10.10.1.10/32` and ICMP Echo Requests from `10.20.1.10/32`.
- **App-B:** Recorded inbound rules allowed SSH (TCP 22) from `10.10.1.10/32` and ICMP Echo Requests from `10.10.2.10/32`.

The source rules above reflect the lab inventory. The bastion's private IP was not recorded, so the SSH source values have not been inferred.

## Connectivity Tests and Results

- **Baseline test:** Ping failed before peering connectivity was fully configured.
- **Forward test:** App-A (`10.10.2.10`) to App-B (`10.20.1.10`) succeeded with four replies and 0% packet loss.
- **Reverse test:** App-B (`10.20.1.10`) to App-A (`10.10.2.10`) succeeded with four replies and 0% packet loss.
- **Private SSH access:** SSH access to App-B was demonstrated through the bastion host.
- **Controlled failure and recovery:** ICMP connectivity was deliberately blocked, the ping failure was observed, and connectivity returned after the relevant rule was restored.

Test evidence is available in the `screenshots/` folder and in the project report PDF.

## Forward and Return Traffic Path

Traffic from App-A to App-B is routed from VPC A's private route table to the VPC peering connection using the destination CIDR `10.20.0.0/16`. The return path uses VPC B's route to `10.10.0.0/16` through the same peering connection. Testing in both directions verified that routes and security rules supported bidirectional private communication.

## Cleanup

After collecting the test evidence, the lab resources were removed. The recorded cleanup confirms that all three EC2 instances were terminated; no leftover EBS volumes, snapshots, or Elastic IPs were found; and the peering connection, custom route tables, subnets, security groups, Internet Gateway, and both VPCs were deleted.

## Project Report

The detailed report is named:

`Sanjana_Katageri_EWB_VPC_Peering.pdf`

It includes the project overview, architecture, resource inventory, route tables, security group rules, connectivity evidence, cleanup, and learning summary.

## Repository Contents

- `architecture/` — architecture diagram
- `screenshots/` — project test and cleanup evidence
- `resource-details/` — resource inventory
- `Sanjana_Katageri_EWB_VPC_Peering.pdf` — project report

## Key Learnings

- Planning VPC and subnet CIDR ranges
- Configuring VPC peering and routes in both directions
- Understanding route tables and subnet associations
- Applying security-group rules for SSH and ICMP
- Validating connectivity with positive and negative tests
- Cleaning up cloud resources after a lab

---

**Security note:** No private keys, passwords, AWS credentials, or access tokens should be committed to this repository.
