# Infrastructure Setup

## Overview

This project runs on **Google Cloud Platform (GCP)** using Virtual Machines (VMs).  
Each service is deployed on a dedicated VM, connected via private IPs inside the same VPC network.  
A Load Balancer VM is responsible for handling all external traffic.

## Servers

### 1. Load Balancer
- **Name:** load-balancer  
- **Machine type:** e2-small (2 vCPU, 2 GB RAM)  
- **OS:** Ubuntu 22.04 LTS  
- **Role:**  
  - Exposes a static public IP  
  - Manages SSL certificates (via Certbot)  
  - Acts as reverse proxy (Nginx) to backend services (Teleport, GitLab, Rancher…)

### 2. Teleport
- **Name:** teleport  
- **Machine type:** e2-small (2 vCPU, 2 GB RAM)  
- **OS:** Ubuntu 22.04 LTS  
- **Role:**  
  - Provides secure access management (SSH & Web UI)  
  - Accessible only through the Load Balancer  

### 3. GitLab
- **Name:** gitlab  
- **Machine type:** e2-standard-4 (4 vCPU, 16 GB RAM)  
- **OS:** Ubuntu 22.04 LTS  
- **Role:**  
  - Git repository management  
  - CI/CD pipelines  
  - Container registry  
  - Accessible only through the Load Balancer  

## Networking

- **VPC:** Default VPC with private subnets  
- **Private communication:** VMs communicate via internal IPs (10.x.x.x range)  
- **Public access:** Only the Load Balancer has a static public IP  
- **Domain:** `mtritran.click` configured via Cloudflare DNS