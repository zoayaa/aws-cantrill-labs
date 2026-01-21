# VPC (Virtual Private Cloud) — Lab Documentation

## Objective  

Understand how AWS VPC works by:
- Creating a custom VPC  
- Understanding CIDR blocks  
- Working with subnets (public and private)  
- Configuring Route Tables  
- Attaching an Internet Gateway  
- Understanding NAT Gateway vs Internet Gateway  
- Learning how networking works in AWS  

# Architecture Diagram 

Subnet Design (CIDR Allocation):

Each tier is isolated per AZ to ensure fault tolerance.
```
┌────────────────────────────────────────────────────────┐
│  VPC: 10.16.0.0/16 (us-east-1)                         |
│                                                        |
│  AZ A               AZ B               AZ C            │
│  ┌────────────┐    ┌──────────────┐   ┌──────────────┐ |
│  │Reserved    │    │Reserved      │   | Reserved     │ │
│  │10.16.0.0/20│    │10.16.64.0/20 │   │10.16.128.0/20│ │
│  └────────────┘    └──────────────┘   └──────────────┘ │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │DB           │  │DB            │  │DB            │   │
│  │10.16.16.0/20│  │10.16.80.0/20 │  │10.16.144.0/20│   │
│  └─────────────┘  └──────────────┘  └──────────────┘   │
│  ┌─────────┐       ┌─────────┐       ┌─────────┐       │
│  │App      │       │App      │       │App      │       │
│  │10.16.32.0/20│   │10.16.96.0/20│   │10.16.160.0/20│  │
│  └─────────┘       └─────────┘       └─────────┘       │
│  ┌─────────┐       ┌─────────┐       ┌─────────┐       │
│  │Web      │       │Web      │       │Web      │       │
│  │10.16.48.0/20│   │10.16.112.0/20│  │10.16.176.0/20│  │
│  │NAT GW   │       │NAT GW   │       │NAT GW   │       │
│  │         │       │Bastion  │       │         │       │
│  └─────────┘       └─────────┘       └─────────┘       │
│                  ┌────────────┐                        │
│                  │ VPC Router │                        │
│                  └─────┬──────┘                        │
│________________________|_______________________________|
│   _________________________________________________    |
|   |              ┌────────────┐                    |   │
│   |              │ Internet GW│   AWS Public       |   │
│   |              └─────┬──────┘     zone           |   │
│   |____________________|___________________________|   │ 
|                        │                               | 
│                     Internet                           │
└─────────────────────────────────────────────────────────
```

# AWS VPC Sizing & Networking

## Overview

* Region: us-east-1
* VPC CIDR: `10.16.0.0/16`
* Architecture: 3-tier (Web, App, DB)
* Availability: 3 Availability Zones
* Design Goal: High availability with room for future expansion

---

## Routing

* Internet Gateway (IGW) attached to the VPC
* Public Web subnets:
  `0.0.0.0/0 → IGW`
* Private App & DB subnets:
  `0.0.0.0/0 → NAT Gateway (same AZ)`
---

## Access Model

* Bastion Host deployed in a public Web subnet
* Secure SSH access to private App and DB subnets
* No direct internet access to private resources
---

Public Subnet       Private Subnet
(10.0.1.0/24)        (10.0.2.0/24)
|                    |
EC2                 Resources


##  What I Built in AWS (Lab Summary)

In the AWS Console, I created:

- 1 Custom VPC with CIDR: `10.0.0.0/16`  
- 1 Internet Gateway and attached it to the VPC  
- 1 Public Subnet  
- 1 Private Subnet  
- 1 Route Table for the Public Subnet  
- Associated the route table with the public subnet  
- Added a route to the Internet Gateway (`0.0.0.0/0 → IGW`)  

---

##  Step-by-Step (Console Work)

### Step 1 — Create VPC  
1. Go to VPC Console  
2. Click **Create VPC**  
3. Set CIDR block: `10.0.0.0/16`  
4. Name it: `my-vpc`  

### Step 2 — Create Subnets  

**Public Subnet**
- CIDR: `10.0.1.0/24`
- Availability Zone: `ap-south-1a`

**Private Subnet**
- CIDR: `10.0.2.0/24`
- Availability Zone: `ap-south-1a`

### Step 3 — Attach Internet Gateway  
- Create Internet Gateway  
- Attach it to `my-vpc`

### Step 4 — Configure Route Table  
- Create a new route table  
- Add route:

Destination: 0.0.0.0/0
Target: Internet Gateway

- Associate this route table with the **public subnet**


##  Learnings & Insights  

- A VPC is a logically isolated network inside AWS and is region resillant service.
- AWS allows VPC sizes from /28 (16 IPs) to /16 (65,536 IPs).
- Public subnets require an Internet Gateway and a route to `0.0.0.0/0`.  
- Private subnets do NOT have direct internet access.  
- Route tables determine where network traffic goes.  
- CIDR blocks define how many IP addresses are available in a network.
- Subnets divide a VPC into smaller networks, usually mapped to Availability Zones.
- NAT Gateway is used when private resources need outbound internet access.  

##  Real-World Use Case  

In real cloud environments:
- Web servers are placed in **public subnets**  
- Databases are placed in **private subnets**  
- Security is controlled using Security Groups and NACLs  
- Internet Gateway is used for public access  
- NAT Gateway is used for secure outbound traffic from private subnets
- Separating Web, App, and DB improves security
