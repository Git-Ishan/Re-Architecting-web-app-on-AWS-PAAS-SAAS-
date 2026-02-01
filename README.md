# VProfile Re-Architecture on AWS (PaaS & SaaS)

## Project Overview

VProfile is a multi-tier Java-based web application that simulates a real-world social networking platform. This project represents **Phase-2** of the VProfile journey, where the application was **re-architected on AWS** by moving from a traditional **IaaS-based Lift & Shift model** to a **PaaS and SaaS-driven cloud-native architecture**.

The primary goal of this phase was to **reduce operational overhead**, improve scalability and reliability, and leverage **managed AWS services** instead of self-managed EC2 instances.

---

## Project Objectives

* Re-architect the application to use **managed AWS services**
* Eliminate manual server and OS management
* Improve scalability, availability, and security
* Implement a production-style deployment using Elastic Beanstalk
* Integrate CDN and HTTPS for performance and security

---

## High-Level Architecture

The re-architected solution uses AWS-managed services for both the application and backend layers.

### Traffic Flow

Users → Route 53 → CloudFront (CDN) → Application Load Balancer → Elastic Beanstalk (Tomcat) → Backend Services

---

## Architecture Diagram

<img width="1536" height="1024" alt="Real architecture" src="https://github.com/user-attachments/assets/14206754-1ea6-4fe2-94c4-4d88c4464fd9" />

---

## AWS Services Used

### Application Layer (PaaS)

* **Elastic Beanstalk**

  * Manages EC2, ALB, Auto Scaling, health checks, and deployments
  * Platform: Tomcat 10 with Amazon Corretto 21

### Backend Layer (SaaS / Managed Services)

* **Amazon RDS (MySQL)** – Managed relational database
* **Amazon ElastiCache (Memcached)** – Managed caching layer
* **Amazon MQ (RabbitMQ / ActiveMQ)** – Managed message broker

### Networking & Delivery

* **Amazon Route 53** – DNS management
* **Amazon CloudFront** – Content Delivery Network (CDN)
* **AWS Certificate Manager (ACM)** – HTTPS certificates

### Monitoring & Security

* **Security Groups** – SG-to-SG based access control
* **CloudWatch** – Logs and metrics (via managed services)

---

## Elastic Beanstalk Configuration

* Environment Type: Load-balanced, auto-scaled
* Platform: Tomcat 10 on Amazon Linux
* Deployment Strategy: Immutable / Traffic Splitting (Canary-ready)
* Health Monitoring: Enabled

<img width="2152" height="1116" alt="elastic beanstalk" src="https://github.com/user-attachments/assets/6a2a0eb9-2e40-4e04-8e65-1065ad0eff04" />

---

## Deployment Strategy

Elastic Beanstalk abstracts the complete deployment pipeline:

* No manual EC2 provisioning
* No manual ALB or ASG creation
* Automated health checks and rollback

Supported deployment policies include:

* All at once
* Rolling
* Rolling with additional batch
* Immutable
* Traffic splitting (Canary deployments)

<img width="997" height="1168" alt="deployment policies" src="https://github.com/user-attachments/assets/3df7bc5f-bdf2-4175-bb91-fe982863fef4" />

---

## Artifact Build & Deployment

* Source code cloned from GitHub
* Application built locally using Maven
* Artifact generated using:

  ```bash
  mvn install
  ```
* WAR file uploaded and deployed via Elastic Beanstalk

<img width="2044" height="1265" alt="build artifact" src="https://github.com/user-attachments/assets/21c19ecd-4d12-4166-a928-3a7a2e48b69c" />

---

## Database Layer – Amazon RDS

* Engine: MySQL Community Edition
* Instance Type: db.t4g.micro
* Deployed in private subnets
* Parameter Group and Subnet Group configured
* Database initialized using a **temporary EC2 instance**

<img width="2146" height="1003" alt="RDS" src="https://github.com/user-attachments/assets/d8835b1e-5352-4605-8ff2-539ae4e8ab36" />

---

## Caching Layer – Amazon ElastiCache

* Engine: Memcached
* Managed cluster deployment
* Integrated with the application using endpoint configuration
* Reduced database load and improved response time

<img width="2142" height="984" alt="elastic cache" src="https://github.com/user-attachments/assets/e20f7c9c-e5ce-42a5-b8de-74765802afd2" />


---

## Messaging Layer – Amazon MQ

* Broker Engine: RabbitMQ (ActiveMQ-compatible)
* Managed message broker
* Private accessibility (no public endpoint)
* Integrated with the application for asynchronous messaging

<img width="2110" height="1131" alt="amazon mq" src="https://github.com/user-attachments/assets/1cca6924-0d98-45e9-96ab-36bea3be47db" />

---

## Content Delivery Network – CloudFront

* CloudFront configured in front of the Beanstalk ALB
* Caches static content (CSS, JS, images)
* Reduces latency and load on backend
* Integrated with custom domain and HTTPS

<img width="2125" height="991" alt="cloud front" src="https://github.com/user-attachments/assets/c476866b-0a22-4d2f-bb6f-f00a4f4407df" />

---

## Security Design

* Backend services deployed in private subnets
* No public access to RDS, ElastiCache, or Amazon MQ
* Security Groups allow **only internal traffic** from Beanstalk instances
* HTTPS enabled end-to-end using ACM certificates

<img width="1773" height="874" alt="security group" src="https://github.com/user-attachments/assets/d0a7b7e1-f079-472a-a205-93ea8ecd928a" />

---

## HTTPS Validation

* Custom domain mapped to CloudFront
* TLS certificate managed by AWS ACM
* Secure access verified via browser

<img width="2155" height="1169" alt="login page with https" src="https://github.com/user-attachments/assets/83f9d9bf-b2ae-4152-ac6e-07492b3d480d" />

---

## Application Validation

* Login page accessible via CloudFront domain
* Database connectivity verified
* Cache and messaging services functional
* CloudFront confirmed via response headers (Hit/Miss)

<img width="2172" height="936" alt="Screenshot 2026-02-01 152450" src="https://github.com/user-attachments/assets/1a99e336-36f5-47af-81e9-810b9b1dac0d" />

---

## Comparison: Lift & Shift vs Re-Architecture

| Aspect         | Lift & Shift     | Re-Architecture   |
| -------------- | ---------------- | ----------------- |
| Cloud Model    | IaaS             | PaaS + SaaS       |
| App Deployment | Manual on EC2    | Elastic Beanstalk |
| Load Balancer  | Manual ALB       | Managed           |
| Scaling        | Manual ASG       | Managed           |
| Database       | MySQL on EC2     | Amazon RDS        |
| Cache          | Memcached on EC2 | ElastiCache       |
| Messaging      | RabbitMQ on EC2  | Amazon MQ         |
| Ops Overhead   | High             | Low               |

---

## Key Learnings

* Difference between IaaS and PaaS/SaaS architectures
* Benefits of managed services in production
* Real-world deployment strategies (Immutable, Canary)
* Importance of CDN and HTTPS
* Secure VPC-based service communication

---

*This project demonstrates a complete re-architecture of a traditional application into a cloud-native AWS solution using managed services.*
