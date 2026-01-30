# Construction Risk Analytics - Solution Architecture Documentation

## 1. Architecture Overview

| Aspect | Details |
|--------|---------|
| **Architecture Pattern** | Microservices + Event-Driven Architecture |
| **Direction** | Left-to-Right flow |
| **Total Layers** | 7 (1 sidebar + 6 horizontal) |
| **Total Components** | 33 nodes |
| **Primary Flow** | Edge → Data → AI/ML → Application → Integration → Experience |
| **Cloud Deployment** | Multi-cloud ready (Azure, AWS, GCP) |
| **Processing Models** | Real-time streaming + Batch + Event-driven + ML inference |
| **Security Model** | Zero-trust with sidebar security layer |

---

## 2. Architecture Layers (Containers)

| Layer ID | Layer Name | Position | Type | Width | Columns | Accent Color | Purpose |
|----------|-----------|----------|------|-------|---------|--------------|---------|
| **security** | Security | Left Sidebar | Sidebar | 150px | 1 | #dc2626 (Red) | Cross-cutting security controls |
| **edge** | Edge | Layer 1 | Horizontal | 160px | 1 | #8b5cf6 (Purple) | Edge computing and ingestion |
| **data** | Data | Layer 2 | Horizontal | 160px | 1 | #10b981 (Green) | Data storage and processing |
| **ai** | AI/ML | Layer 3 | Horizontal | 160px | 1 | #f59e0b (Orange) | Artificial intelligence and ML |
| **app** | Application | Layer 4 | Horizontal | 320px | 2 | #3b82f6 (Blue) | Application services and runtime |
| **integration** | Integration | Layer 5 | Horizontal | 320px | 2 | #a855f7 (Purple) | Integration services and DevOps |
| **ux** | Experience | Layer 6 | Horizontal | 450px | 2 | #ec4899 (Pink) | User experience and interfaces |

---

## 3. Components by Layer

### 3.1 Security Layer (6 components - Sidebar)

| ID | Label | Row | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|----------|-------------|-------------------|------------------|------------------|
| **sec-identity** | Identity | 0 | SSO/MFA | Single sign-on and multi-factor authentication | Azure AD + MFA | AWS IAM + Cognito | Cloud Identity + MFA |
| **sec-secrets** | Secrets | 1 | Vault | Secrets and key management | Azure Key Vault | AWS Secrets Manager | Secret Manager |
| **sec-policy** | Policy | 2 | Compliance | Policy enforcement and compliance | Azure Policy | AWS Config | Cloud Policy |
| **sec-threat** | Threat | 3 | Detection | Threat detection and prevention | Azure Defender | AWS GuardDuty | Security Command Center |
| **sec-network** | Network | 4 | Security | Network security and firewalls | Azure Firewall | AWS WAF | Cloud Armor |
| **sec-observe** | Observe | 5 | Logs | Observability and logging | Azure Monitor | CloudWatch | Cloud Logging |

**Notes:**
- Sidebar layout provides cross-cutting security
- All layers connect to appropriate security components
- Zero-trust security model

---

### 3.2 Edge Layer (4 components)

| ID | Label | Row | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|----------|-------------|-------------------|------------------|------------------|
| **edge-iot** | IoT | 0 | 50K devices | IoT device management and telemetry | Azure IoT Hub | AWS IoT Core | Cloud IoT Core |
| **edge-stream** | Streaming | 1 | Events | Real-time event streaming | Azure Event Hubs | Amazon Kinesis | Cloud Pub/Sub |
| **edge-api** | API GW | 2 | Gateway | API gateway and routing | Azure API Management | Amazon API Gateway | Cloud API Gateway |
| **edge-workflow** | Workflow | 3 | ETL | Workflow orchestration and ETL | Azure Data Factory | AWS Step Functions | Cloud Composer |

**Notes:**
- Edge layer handles ingestion from 50K IoT devices
- Event streaming for real-time processing
- API gateway for application access

---

### 3.3 Data Layer (4 components)

| ID | Label | Row | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|----------|-------------|-------------------|------------------|------------------|
| **data-lake** | Lake | 0 | Storage | Data lake for raw and processed data | Azure Data Lake Gen2 | Amazon S3 | Cloud Storage |
| **data-warehouse** | Warehouse | 1 | OLAP | Data warehouse for analytics | Azure Synapse | Amazon Redshift | BigQuery |
| **data-nosql** | NoSQL | 2 | Operational | NoSQL database for operational data | Azure Cosmos DB | Amazon DynamoDB | Cloud Firestore |
| **data-spark** | Spark | 3 | Processing | Distributed data processing | Azure Databricks | Amazon EMR | Cloud Dataproc |

**Notes:**
- Multi-storage strategy: lake, warehouse, NoSQL
- Spark for distributed processing
- Supports both OLTP and OLAP workloads

---

### 3.4 AI/ML Layer (4 components)

| ID | Label | Row | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|----------|-------------|-------------------|------------------|------------------|
| **ai-ml** | ML | 0 | Training | Machine learning model training | Azure ML | Amazon SageMaker | Vertex AI |
| **ai-llm** | LLM | 1 | GPT-4 | Large language models | Azure OpenAI | Amazon Bedrock | Vertex AI PaLM |
| **ai-vision** | Vision | 2 | Detection | Computer vision and object detection | Azure Computer Vision | Amazon Rekognition | Cloud Vision API |
| **ai-vector** | Vector | 3 | Search | Vector database for similarity search | Azure Cognitive Search | Amazon OpenSearch | Vertex AI Vector Search |

**Notes:**
- Multi-model AI: Traditional ML, LLMs, Computer Vision
- Vector search for semantic similarity
- GPT-4 integration for conversational AI

---

### 3.5 Application Layer (6 components - 2x3 Grid)

| ID | Label | Row | Col | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|-----|----------|-------------|-------------------|------------------|------------------|
| **app-k8s** | K8s | 0 | 0 | Containers | Kubernetes container orchestration | Azure Kubernetes Service | Amazon EKS | Google Kubernetes Engine |
| **app-func** | Functions | 0 | 1 | Serverless | Serverless functions | Azure Functions | AWS Lambda | Cloud Functions |
| **app-ws** | WebSocket | 1 | 0 | Real-time | WebSocket connections for real-time | Azure SignalR | AWS AppSync | Cloud Run WebSockets |
| **app-web** | Web | 1 | 1 | Hosting | Static web hosting | Azure Static Web Apps | AWS Amplify | Cloud Storage + CDN |
| **app-notify** | Notify | 2 | 0 | Push | Push notifications | Azure Notification Hubs | Amazon SNS | Firebase Cloud Messaging |
| **app-cache** | Cache | 2 | 1 | Redis | Distributed caching | Azure Cache for Redis | Amazon ElastiCache | Cloud Memorystore |

**Notes:**
- Hybrid compute: Containers (K8s) + Serverless (Functions)
- Real-time capabilities via WebSocket
- Distributed caching for performance

---

### 3.6 Integration Layer (5 components - 2 Columns)

| ID | Label | Row | Col | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|-----|----------|-------------|-------------------|------------------|------------------|
| **int-queue** | Queue | 0 | 0 | Messages | Message queue for async processing | Azure Service Bus | Amazon SQS | Cloud Tasks |
| **int-event** | Events | 0 | 1 | Router | Event routing and pub/sub | Azure Event Grid | Amazon EventBridge | Cloud Pub/Sub |
| **int-bi** | BI | 1 | 0 | Reports | Business intelligence and reporting | Power BI | Amazon QuickSight | Looker |
| **int-cicd** | CI/CD | 1 | 1 | Pipeline | Continuous integration and deployment | Azure DevOps | AWS CodePipeline | Cloud Build |
| **int-apm** | APM | 2 | 0 | Monitoring | Application performance monitoring | Azure Application Insights | AWS X-Ray | Cloud Trace |

**Notes:**
- Event-driven architecture with queue and event router
- DevOps integration with CI/CD pipelines
- APM for application observability

---

### 3.7 Experience Layer (4 components - 2x2 Grid)

| ID | Label | Row | Col | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|-----|-----|----------|-------------|-------------------|------------------|------------------|
| **ux-dash** | Dashboard | 0 | 0 | Risk KPIs | Real-time risk analytics dashboard | Power BI Embedded | Amazon QuickSight | Looker Studio |
| **ux-mobile** | Mobile | 0 | 1 | iOS/Android | Native mobile applications | Azure Mobile Apps | AWS Amplify | Firebase |
| **ux-bot** | Bot | 1 | 0 | Chat AI | Conversational AI chatbot | Azure Bot Service | Amazon Lex | Dialogflow CX |
| **ux-cdn** | CDN | 1 | 1 | Edge | Content delivery network | Azure CDN | Amazon CloudFront | Cloud CDN |

**Notes:**
- Multi-channel experience: Web dashboard, Mobile, Chatbot
- CDN for global content delivery
- AI-powered chatbot for natural language interaction

---

## 4. Flow Scenarios

**Overview:**
The solution architecture supports 4 primary flow scenarios demonstrating different data and processing patterns.

**Flow Scenarios:**
- **Real-time Flow** (green) - IoT sensors to real-time dashboard
- **API Flow** (blue) - CDN to API gateway to application services
- **ML Flow** (orange) - Data warehouse to ML training to vector search
- **Events Flow** (purple) - Application to event router to notifications

**Connection Types:**
- **Data connections** (solid green lines, 2px): Bulk data transfer
- **Sync connections** (solid gray lines, 1.5px): Synchronous request/response
- **Async connections** (dashed purple lines, 1.5px): Asynchronous messaging

---

### 4.1 Real-time Flow Scenario

**Path:** IoT → Streaming → Data Lake → ML → K8s → Dashboard

**Use Cases:**
- Real-time sensor data from construction sites
- Live risk scoring and alerting
- Streaming analytics for safety monitoring

**Flow Details:**

| # | From | To | Type | Latency | Purpose |
|---|------|-----|------|---------|---------|
| 1 | **IoT Devices** | **Streaming** | Async | < 100ms | Device telemetry ingestion |
| 2 | **Streaming** | **Data Lake** | Data | < 500ms | Persist raw event data |
| 3 | **Data Lake** | **ML Training** | Data | Batch | Train risk models |
| 4 | **ML Training** | **K8s App** | Sync | < 50ms | Model inference API |
| 5 | **K8s App** | **Dashboard** | Sync | < 100ms | Real-time KPI updates |

**Flow Characteristics:**
- **End-to-end Latency:** < 1 second
- **Throughput:** 50K devices × 1 msg/sec = 50K events/sec
- **Use Case:** Live construction monitoring

---

### 4.2 API Flow Scenario

**Path:** CDN → API Gateway → K8s → NoSQL

**Use Cases:**
- Mobile app API requests
- Web application data access
- Third-party integrations

**Flow Details:**

| # | From | To | Type | Latency | Purpose |
|---|------|-----|------|---------|---------|
| 1 | **CDN** | **API Gateway** | Sync | < 50ms | Edge routing |
| 2 | **API Gateway** | **Identity** | Sync | < 20ms | Authentication/authorization |
| 3 | **API Gateway** | **K8s App** | Sync | < 30ms | Request routing |
| 4 | **K8s App** | **NoSQL** | Sync | < 10ms | Data retrieval |

**Flow Characteristics:**
- **End-to-end Latency:** < 110ms
- **Pattern:** Request/response with caching
- **Use Case:** Mobile and web application APIs

---

### 4.3 ML Flow Scenario

**Path:** Data Warehouse → ML Training → Vector Search → K8s

**Use Cases:**
- Weekly risk model retraining
- Semantic search for similar projects
- Predictive analytics

**Flow Details:**

| # | From | To | Type | Latency | Purpose |
|---|------|-----|------|---------|---------|
| 1 | **Data Warehouse** | **ML Training** | Data | Batch | Historical data for training |
| 2 | **ML Training** | **Vector Search** | Sync | < 100ms | Embed trained features |
| 3 | **Vector Search** | **K8s App** | Sync | < 50ms | Similarity search API |

**Flow Characteristics:**
- **Training Frequency:** Weekly batch
- **Model Types:** Risk prediction, anomaly detection
- **Use Case:** Predictive risk analytics

---

### 4.4 Events Flow Scenario

**Path:** K8s → Event Router → Notifications

**Use Cases:**
- Real-time alerts for risk thresholds
- System event notifications
- Workflow automation

**Flow Details:**

| # | From | To | Type | Latency | Purpose |
|---|------|-----|------|---------|---------|
| 1 | **K8s App** | **Event Router** | Async | < 20ms | Publish events |
| 2 | **Event Router** | **Notifications** | Async | < 100ms | Push notifications |
| 3 | **Event Router** | **Dashboard** | Async | < 100ms | Dashboard updates |

**Flow Characteristics:**
- **Pattern:** Pub/sub event-driven
- **Decoupling:** Loose coupling between services
- **Use Case:** Real-time notifications and alerts

---

## 5. Connection Types and Patterns

| Connection Type | Visual Style | Stroke Color | Stroke Width | Pattern | Purpose | Example |
|----------------|--------------|--------------|--------------|---------|---------|---------|
| **Data** | Solid line | Green (#10b981) | 2px | Solid | Bulk data transfer | Streaming → Data Lake |
| **Sync** | Solid line | Gray (#94a3b8) | 1.5px | Solid | Request/response | API Gateway → K8s |
| **Async** | Dashed line | Purple (#8b5cf6) | 1.5px | Dash: 5,3 | Asynchronous messaging | K8s → Event Router |

**Connection Patterns:**

### Data Connections (11 total)
- Edge → Data: 2 connections (streaming, workflow)
- Data internal: 2 connections (lake → warehouse, warehouse → BI)
- Data → AI: 3 connections (lake → ML, warehouse → ML, spark → ML)
- AI internal: 2 connections (ML → vector, LLM → vector)
- Data → App: 2 connections (NoSQL → K8s, NoSQL → Cache)

### Sync Connections (13 total)
- Edge → App: 2 connections (API → K8s, API → Identity)
- AI → App: 2 connections (LLM → K8s, Vision → K8s)
- App → Integration: 1 connection (K8s → Dashboard)
- App → UX: 3 connections (K8s → Dashboard, WebSocket → Dashboard, WebSocket → Mobile)
- Integration → UX: 1 connection (BI → Dashboard)
- UX → Edge: 2 connections (CDN → API, Bot → LLM)
- Data → Integration: 1 connection (Warehouse → BI)
- Data → App: 1 connection (NoSQL → Cache)

### Async Connections (8 total)
- Edge: 2 connections (IoT → Streaming, Streaming → Functions)
- App → Integration: 3 connections (K8s → Queue, K8s → Event, Functions → Event)
- Integration → App: 1 connection (Event → Notify)
- Integration → UX: 1 connection (Event → Dashboard)

**TOTAL: 32 connections**

---

## 6. Technology Stack by Cloud Provider

### 6.1 Microsoft Azure Stack

| Layer | Component | Azure Service | Service Category | Pricing Tier Suggestion |
|-------|-----------|---------------|------------------|------------------------|
| Security | Identity | Azure AD + MFA | Identity | Premium P2 |
| Security | Secrets | Azure Key Vault | Security | Standard |
| Security | Policy | Azure Policy | Governance | Standard |
| Security | Threat | Azure Defender | Security | Standard |
| Security | Network | Azure Firewall | Network | Premium |
| Security | Observe | Azure Monitor | Monitoring | Pay-per-GB |
| Edge | IoT | Azure IoT Hub | IoT | S1 (50K devices) |
| Edge | Streaming | Azure Event Hubs | Messaging | Standard |
| Edge | API GW | Azure API Management | Integration | Developer/Standard |
| Edge | Workflow | Azure Data Factory | Data | Pay-per-run |
| Data | Lake | Azure Data Lake Gen2 | Storage | Hot tier |
| Data | Warehouse | Azure Synapse | Analytics | DW500c |
| Data | NoSQL | Azure Cosmos DB | Database | Provisioned 10K RU/s |
| Data | Spark | Azure Databricks | Analytics | Standard |
| AI | ML | Azure ML | AI/ML | Standard |
| AI | LLM | Azure OpenAI | AI/ML | Pay-per-token |
| AI | Vision | Azure Computer Vision | AI/ML | S1 |
| AI | Vector | Azure Cognitive Search | Search | Standard |
| App | K8s | Azure Kubernetes Service | Compute | 3 node pool |
| App | Functions | Azure Functions | Serverless | Consumption |
| App | WebSocket | Azure SignalR | Communication | Standard |
| App | Web | Azure Static Web Apps | Hosting | Standard |
| App | Notify | Azure Notification Hubs | Messaging | Standard |
| App | Cache | Azure Cache for Redis | Cache | C1 (1GB) |
| Integration | Queue | Azure Service Bus | Messaging | Standard |
| Integration | Events | Azure Event Grid | Messaging | Pay-per-event |
| Integration | BI | Power BI | Analytics | Pro |
| Integration | CI/CD | Azure DevOps | DevOps | Basic + 5 users |
| Integration | APM | Azure Application Insights | Monitoring | Pay-per-GB |
| UX | Dashboard | Power BI Embedded | Analytics | A1 SKU |
| UX | Mobile | Azure Mobile Apps | Mobile | Free |
| UX | Bot | Azure Bot Service | AI | S1 |
| UX | CDN | Azure CDN | CDN | Standard Microsoft |

---

### 6.2 AWS Stack

| Layer | Component | AWS Service | Service Category | Pricing Tier Suggestion |
|-------|-----------|-------------|------------------|------------------------|
| Security | Identity | AWS IAM + Cognito | Identity | Pay-per-user |
| Security | Secrets | AWS Secrets Manager | Security | Pay-per-secret |
| Security | Policy | AWS Config | Governance | Pay-per-rule |
| Security | Threat | AWS GuardDuty | Security | Pay-per-GB |
| Security | Network | AWS WAF | Network | Pay-per-rule |
| Security | Observe | CloudWatch | Monitoring | Pay-per-GB |
| Edge | IoT | AWS IoT Core | IoT | Pay-per-message |
| Edge | Streaming | Amazon Kinesis | Streaming | On-demand |
| Edge | API GW | Amazon API Gateway | Integration | Pay-per-request |
| Edge | Workflow | AWS Step Functions | Orchestration | Pay-per-transition |
| Data | Lake | Amazon S3 | Storage | Standard tier |
| Data | Warehouse | Amazon Redshift | Analytics | dc2.large |
| Data | NoSQL | Amazon DynamoDB | Database | On-demand |
| Data | Spark | Amazon EMR | Analytics | m5.xlarge |
| AI | ML | Amazon SageMaker | AI/ML | ml.m5.xlarge |
| AI | LLM | Amazon Bedrock | AI/ML | Pay-per-token |
| AI | Vision | Amazon Rekognition | AI/ML | Pay-per-image |
| AI | Vector | Amazon OpenSearch | Search | t3.small |
| App | K8s | Amazon EKS | Compute | t3.medium |
| App | Functions | AWS Lambda | Serverless | Pay-per-invocation |
| App | WebSocket | AWS AppSync | Real-time | Pay-per-request |
| App | Web | AWS Amplify | Hosting | Pay-per-GB |
| App | Notify | Amazon SNS | Messaging | Pay-per-notification |
| App | Cache | Amazon ElastiCache | Cache | cache.t3.micro |
| Integration | Queue | Amazon SQS | Messaging | Pay-per-request |
| Integration | Events | Amazon EventBridge | Events | Pay-per-event |
| Integration | BI | Amazon QuickSight | Analytics | Enterprise |
| Integration | CI/CD | AWS CodePipeline | DevOps | Pay-per-pipeline |
| Integration | APM | AWS X-Ray | Monitoring | Pay-per-trace |
| UX | Dashboard | Amazon QuickSight | Analytics | Enterprise |
| UX | Mobile | AWS Amplify | Mobile | Pay-per-MAU |
| UX | Bot | Amazon Lex | AI | Pay-per-request |
| UX | CDN | Amazon CloudFront | CDN | Pay-per-GB |

---

### 6.3 Google Cloud Platform Stack

| Layer | Component | GCP Service | Service Category | Pricing Tier Suggestion |
|-------|-----------|-------------|------------------|------------------------|
| Security | Identity | Cloud Identity + MFA | Identity | Enterprise |
| Security | Secrets | Secret Manager | Security | Pay-per-secret |
| Security | Policy | Cloud Policy | Governance | Free |
| Security | Threat | Security Command Center | Security | Premium |
| Security | Network | Cloud Armor | Network | Pay-per-rule |
| Security | Observe | Cloud Logging | Monitoring | Pay-per-GB |
| Edge | IoT | Cloud IoT Core | IoT | Pay-per-MB |
| Edge | Streaming | Cloud Pub/Sub | Messaging | Pay-per-GB |
| Edge | API GW | Cloud API Gateway | Integration | Pay-per-request |
| Edge | Workflow | Cloud Composer | Orchestration | Small environment |
| Data | Lake | Cloud Storage | Storage | Standard |
| Data | Warehouse | BigQuery | Analytics | On-demand |
| Data | NoSQL | Cloud Firestore | Database | Pay-per-operation |
| Data | Spark | Cloud Dataproc | Analytics | n1-standard-2 |
| AI | ML | Vertex AI | AI/ML | n1-standard-4 |
| AI | LLM | Vertex AI PaLM | AI/ML | Pay-per-token |
| AI | Vision | Cloud Vision API | AI/ML | Pay-per-image |
| AI | Vector | Vertex AI Vector Search | Search | Pay-per-query |
| App | K8s | Google Kubernetes Engine | Compute | n1-standard-2 |
| App | Functions | Cloud Functions | Serverless | Pay-per-invocation |
| App | WebSocket | Cloud Run WebSockets | Real-time | Pay-per-request |
| App | Web | Cloud Storage + CDN | Hosting | Pay-per-GB |
| App | Notify | Firebase Cloud Messaging | Messaging | Free |
| App | Cache | Cloud Memorystore | Cache | M1 (1GB) |
| Integration | Queue | Cloud Tasks | Messaging | Pay-per-task |
| Integration | Events | Cloud Pub/Sub | Events | Pay-per-GB |
| Integration | BI | Looker | Analytics | Developer |
| Integration | CI/CD | Cloud Build | DevOps | Pay-per-build-minute |
| Integration | APM | Cloud Trace | Monitoring | Pay-per-span |
| UX | Dashboard | Looker Studio | Analytics | Free |
| UX | Mobile | Firebase | Mobile | Spark (free) |
| UX | Bot | Dialogflow CX | AI | Pay-per-session |
| UX | CDN | Cloud CDN | CDN | Pay-per-GB |

---

## 7. Architecture Principles

| Principle | Implementation | Benefit |
|-----------|----------------|---------|
| **Microservices** | Independent services in K8s | Scalability, independent deployment |
| **Event-Driven** | Event router with pub/sub | Loose coupling, resilience |
| **Zero-Trust Security** | Sidebar security layer | Defense in depth, least privilege |
| **Multi-Cloud** | Cloud-agnostic design | Avoid vendor lock-in |
| **Serverless-First** | Functions + managed services | Reduced operational overhead |
| **Real-time Processing** | Streaming + WebSocket | Low-latency user experience |
| **AI-Powered** | ML, LLM, Vision | Intelligent automation |
| **Observability** | APM + Logs + Monitoring | Proactive issue detection |

---

## 8. Key Metrics and Specifications

| Metric | Value | Description |
|--------|-------|-------------|
| **IoT Devices** | 50,000 | Active sensor devices |
| **Event Throughput** | 50K events/sec | Real-time event processing |
| **API Latency** | < 110ms | P95 API response time |
| **Real-time Latency** | < 1 second | End-to-end streaming latency |
| **ML Retraining** | Weekly | Model update frequency |
| **Availability SLA** | 99.9% | Target uptime |
| **Total Components** | 33 | Solution components |
| **Total Connections** | 32 | Component interconnections |
| **Security Layers** | 6 | Security control points |

---

## 9. Diagram Features and Interactivity

| Feature | Implementation | Purpose |
|---------|----------------|---------|
| **Sidebar Layout** | Left security sidebar | Cross-cutting security visualization |
| **Multi-Column Layers** | 2-column grids | Compact component arrangement |
| **Connection Types** | 3 types (data, sync, async) | Clear communication patterns |
| **Color Coding** | Layer-specific accent colors | Quick layer identification |
| **Flow Scenarios** | 4 animated flows | Demonstrate data paths |

**Layout Specifications:**
- **Security Sidebar:** 150px width, 8px vertical spacing
- **Node Sizing:** 130×68px (standard), 120×60px (sidebar)
- **Layer Spacing:** Dynamic based on content
- **Diagram Width:** 1800px

---

## 10. Security Architecture

### Security Controls by Layer

| Security Control | Applied To | Implementation | Purpose |
|-----------------|-----------|----------------|---------|
| **Identity (SSO/MFA)** | Edge API, All UX | OAuth 2.0 + MFA | User authentication |
| **Secrets Management** | All services | Vault integration | Credential protection |
| **Policy Enforcement** | All layers | Policy engine | Compliance validation |
| **Threat Detection** | Network traffic | IDS/IPS | Intrusion detection |
| **Network Security** | All connections | Firewall + WAF | Network protection |
| **Observability** | All components | Logs + metrics | Security monitoring |

---

## Summary Statistics

| Metric | Count |
|--------|-------|
| **Total Components** | 33 |
| **Data Connections (solid green)** | 11 |
| **Sync Connections (solid gray)** | 13 |
| **Async Connections (dashed purple)** | 8 |
| **Security Controls** | 6 |
| **Layers** | 7 (1 sidebar + 6 horizontal) |
| **Multi-Column Layers** | 3 (App, Integration, UX) |
| **Flow Scenarios** | 4 (Real-time, API, ML, Events) |

---

## Architecture Summary

This **Construction Risk Analytics - Solution Architecture** implements a comprehensive microservices and event-driven architecture with the following key characteristics:

### Core Design Principles
- **33 total components** across 7 layers (1 sidebar + 6 horizontal)
- **32 connections** (11 data, 13 sync, 8 async) with distinct visual styles
- **4 processing patterns:** Real-time streaming, Request/response, Event-driven, Batch ML
- **6 security controls:** Identity, Secrets, Policy, Threat, Network, Observability
- **Multi-tier architecture:** Edge → Data → AI/ML → Application → Integration → UX
- **Hybrid compute:** Kubernetes + Serverless functions

### Visual Design Features
- **Security sidebar:** Left-side cross-cutting security controls
- **Multi-column layouts:** 2-column grids for Application, Integration, and UX layers
- **Three connection types:** Data (green solid), Sync (gray solid), Async (purple dashed)
- **Flow scenarios:** Visual representation of 4 primary data flows
- **Color-coded layers:** Each layer has distinct accent color

### Technology Stack
The architecture is **cloud-agnostic** and can be deployed on:
- **Microsoft Azure** - Full Azure native stack
- **AWS** - Complete AWS service mapping
- **Google Cloud Platform** - End-to-end GCP implementation

### Use Cases
- **Real-time monitoring:** 50K IoT devices streaming construction site data
- **Predictive analytics:** ML-powered risk prediction and anomaly detection
- **Conversational AI:** GPT-4 powered chatbot for natural language queries
- **Mobile-first:** Native iOS/Android apps with push notifications
- **Event-driven automation:** Automated workflows triggered by risk thresholds

The architecture is designed for **scalability, resilience, and multi-cloud flexibility** while maintaining comprehensive security and observability across all layers.
