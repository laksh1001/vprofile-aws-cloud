# Enterprise Multi-Tier Web Application Deployment on AWS

A production-grade, highly available, and secure migration of a Java multi-tier web application (vProfile) from a local environment to AWS Platform-as-a-Service (PaaS) and Software-as-a-Service (SaaS) ecosystems.

## 🏗️ Architectural Overview

The application architecture decouples layers into distinct, managed cloud services to ensure high availability, fault tolerance, and low global latency:

1. **Web/App Tier:** Java application running on **AWS Elastic Beanstalk** to manage auto-scaling and load balancing automatically.
2. **Database Tier:** **Amazon RDS (MySQL)** handles persistent structured data with automated patching and backups.
3. **Caching Layer:** **Amazon ElastiCache (Memcached)** acts as an in-memory layer to speed up dynamic database queries.
4. **Message Broker:** **Amazon MQ (RabbitMQ)** facilitates asynchronous request processing between decoupled services.
5. **Content Delivery:** **Amazon CloudFront** edge-caches static content globally to minimize load times.
6. **Security:** **AWS Certificate Manager (ACM)** routes traffic securely via HTTPS over a custom domain.

---

## 🛠️ Tech Stack & Services Used

- **Language/Framework:** Java, Spring, Maven
- **Cloud Provider:** Amazon Web Services (AWS)
- **Compute:** Elastic Beanstalk
- **Database & Storage:** RDS (MySQL), ElastiCache (Memcached)
- **Messaging:** Amazon MQ (RabbitMQ)
- **Networking & Content Delivery:** Route 53, CloudFront, Application Load Balancer (ALB)
- **Security:** AWS Certificate Manager (ACM), IAM, Security Groups

---

## 🚀 Key Implementation Steps

### 1. Networking & Security Group Configuration
- Created specialized security groups for backend components (Backend SG, DB SG, Cache SG, RabbitMQ SG) to restrict inbound traffic to specific ports, ensuring tight infrastructure perimeter control.

### 2. Managed Services Provisioning
- Deployed Amazon RDS MySQL database instances.
- Initialized a Memcached cluster on ElastiCache for session handling.
- Configured a fully-managed RabbitMQ instance on Amazon MQ.

### 3. Database Initialization
- Injected backend schemas (`accountsdb.sql`) directly into the active Amazon RDS instance to seed primary relational user tables.

### 4. Application Build & Artifact Deployment
- Configured environment details inside `src/main/resources/application.properties`.
- Package built using Maven to generate a deployable `.war` file.
- Deployed the application artifact onto an AWS Elastic Beanstalk environment.

### 5. SSL/TLS & Global CDN Configuration
- Requested a public SSL/TLS certificate through AWS Certificate Manager.
- Configured a custom domain name mapping to direct web traffic securely over HTTPS.
- Provisioned an Amazon CloudFront distribution pointing to the Elastic Beanstalk load balancer, utilizing explicit cache behaviors to safely distinguish static files from dynamic login paths.



## 🔍 Verification & System Validation

### 1. Cryptographic Handshake Validation
- Successfully established end-to-end HTTPS routing over the custom domain using an active, Amazon-issued SSL/TLS certificate, validating error-free traffic resolution across distinct browser environments.

### 2. Cache-Interception Performance Tracing
- Verified operational caching cycles within the web interface:
  * **Initial Transaction (Cache Miss):** Application retrieves data directly via JDBC from **Amazon RDS** and synchronizes it to memory: `[Data is From DB and Data Inserted In Cache !!]`
  * **Subsequent Transactions (Cache Hit):** High-speed read operations bypass persistent storage completely, pulling query blocks instantly out of volatile RAM via **Amazon ElastiCache**: `[Data is From Cache]`
 
