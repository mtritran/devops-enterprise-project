# DevOps Enterprise Project

## Overview
This project builds the infrastructure and deploys microservices in an end-to-end DevOps workflow on **Google Cloud**.

## Milestones
- **Milestone 1:** Domain + Teleport + Load Balancer (completed on 18/09/2025)  
- **Milestone 2:** GitLab + Rancher + Kubernetes Cluster (in progress)  
- **Milestone 3:** Microservices deployment with Docker Compose (planned)  

## Documentation
- [Infrastructure](docs/infrastructure.md)
- [Domain Setup](docs/setup-domain.md)
- [Teleport Setup](docs/setup-teleport.md)  
- [Load Balancer Setup](docs/setup-lb.md)  

## Results
![Teleport Login](docs/screenshots/teleport-setup.png)

---

## Tech Stack
- **Cloud Provider:** Google Cloud Platform (GCP)  
- **Reverse Proxy & SSL:** Nginx + Let’s Encrypt (Certbot) 
- **Access Management:** Teleport   
- **DNS Management:** Cloudflare  
- **Orchestration & CI/CD:** Docker, Kubernetes, GitLab, Harbor (planned)  
- **Monitoring:** Prometheus + Grafana (planned)  
