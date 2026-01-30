# Construction Risk Analytics - Data Architecture Documentation

## 1. Architecture Overview

| Aspect | Details |
|--------|---------|
| **Architecture Pattern** | Medallion Architecture (Bronze → Silver → Gold) |
| **Direction** | Left-to-Right flow |
| **Total Layers** | 7 (6 horizontal + 1 crosscutting) |
| **Total Components** | 25 nodes |
| **Primary Data Flow** | Sources → Ingestion → Bronze → Silver → Gold → Consumption |
| **Cloud Providers** | Microsoft Azure, AWS, Google Cloud Platform |
| **Data Processing** | Real-time streaming + Batch processing + CDC |
| **Governance Model** | Crosscutting layer spanning entire architecture |

---

## 2. Architecture Layers (Containers)

| Layer ID | Layer Name | Position | Type | Width | Columns | Accent Color | Purpose |
|----------|-----------|----------|------|-------|---------|--------------|---------|
| **sources** | Sources | Layer 1 | Horizontal | 170px | 1 | #64748b (Gray) | External data sources feeding the system |
| **ingestion** | Ingestion | Layer 2 | Horizontal | 170px | 1 | #8b5cf6 (Purple) | Data ingestion mechanisms |
| **bronze** | Bronze | Layer 3 | Horizontal | 170px | 1 | #CD7F32 (Bronze) | Raw data lake - unchanged from source |
| **silver** | Silver | Layer 4 | Horizontal | 170px | 1 | #94a3b8 (Silver) | Cleaned and validated data |
| **gold** | Gold | Layer 5 | Horizontal | 170px | 1 | #ca8a04 (Gold) | Business-ready, curated data |
| **consumption** | Consumption | Layer 6 | Horizontal | 170px | 1 | #3b82f6 (Blue) | Consumer applications and services |
| **governance** | Data Governance | Bottom | Crosscutting | Full width | 4 | #ef4444 (Red) | Cross-cutting governance controls |

---

## 3. Components by Layer

### 3.1 Sources Layer (5 components)

| ID | Label | Role | Sublabel | Icon Type | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) |
|----|-------|------|----------|-----------|-------------|-------------------|------------------|------------------|
| **src-iot** | IoT Sensors | PRIMARY | 50K devices | sensor | IoT sensor data from construction sites | Azure IoT Hub | AWS IoT Core | Cloud IoT Core |
| **src-camera** | Cameras | PRIMARY | Vision | camera | Video surveillance and image data | Azure Computer Vision | Amazon Rekognition | Cloud Vision API |
| **src-docs** | Documents | PRIMARY | PDFs | document | Document management system | Azure Blob Storage | Amazon S3 | Cloud Storage |
| **src-weather** | Weather | PRIMARY | APIs | cloud | External weather API data | Azure Logic Apps | AWS Lambda | Cloud Functions |
| **src-erp** | ERP | PRIMARY | SAP/Oracle | database | Enterprise Resource Planning systems | Azure SQL Database | Amazon RDS | Cloud SQL |

**Notes:**
- All source components are PRIMARY as they are entry points to the system
- 50K IoT devices generating continuous telemetry
- Multiple data formats: structured (ERP), semi-structured (weather APIs), unstructured (documents, images)

---

### 3.2 Ingestion Layer (3 components)

| ID | Label | Role | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) | Processing Type |
|----|-------|------|----------|-------------|-------------------|------------------|------------------|-----------------|
| **ing-stream** | Streaming | PRIMARY | Real-time | Real-time data ingestion for IoT and cameras | Azure Event Hubs | Amazon Kinesis | Cloud Pub/Sub | Stream processing |
| **ing-batch** | Batch | PRIMARY | Scheduled | Scheduled batch processing for documents and weather | Azure Data Factory | AWS Glue | Cloud Dataflow | Batch processing |
| **ing-cdc** | CDC | PRIMARY | Changes | Change Data Capture from ERP systems | Azure Stream Analytics | AWS DMS | Cloud Datastream | Change tracking |

**Notes:**
- All ingestion components are PRIMARY as they handle critical data flow
- Multiple ingestion patterns support different data velocity requirements
- CDC enables real-time synchronization of ERP changes

---

### 3.3 Bronze Layer (3 components)

| ID | Label | Role | Sublabel | Icon | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) | Data Volume |
|----|-------|------|----------|------|-------------|-------------------|------------------|------------------|-------------|
| **brz-lake** | Raw Lake | PRIMARY | 10M/day | data-lake | Raw, unprocessed data storage | Azure Data Lake Gen2 | Amazon S3 | Cloud Storage | 10M records/day |
| **brz-schema** | Schema | SECONDARY | Registry | data-nosql | Schema registry for data validation | Azure Schema Registry | AWS Glue Data Catalog | Cloud Data Catalog | Metadata only |
| **brz-catalog** | Catalog | SECONDARY | Metadata | sec-datgov | Metadata catalog and discovery | Azure Purview | AWS Glue Catalog | Cloud Data Catalog | Metadata only |

**Notes:**
- Bronze = Raw zone - data stored exactly as received from source
- 10 million records processed daily
- Schema registry ensures data contracts
- Secondary components provide supportive metadata management

---

### 3.4 Silver Layer (3 components)

| ID | Label | Role | Sublabel | Icon | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) | Quality Metric |
|----|-------|------|----------|------|-------------|-------------------|------------------|------------------|----------------|
| **slv-lake** | Clean Lake | PRIMARY | 99.5% | data-lake | Cleaned, validated, deduplicated data | Azure Data Lake Gen2 | Amazon S3 | Cloud Storage | 99.5% quality |
| **slv-quality** | Quality | SECONDARY | Validation | sec-policy | Data quality checks and validation rules | Azure Data Quality | AWS Deequ | Cloud DQ | Quality rules |
| **slv-dedup** | Dedup | SECONDARY | Merge | data-spark | Deduplication and entity resolution | Azure Databricks | Amazon EMR | Cloud Dataproc | Dedup engine |

**Notes:**
- Silver = Clean zone - validated, standardized data
- 99.5% data quality threshold maintained
- Deduplication ensures single source of truth
- Secondary components enforce quality and deduplication

---

### 3.5 Gold Layer (3 components)

| ID | Label | Role | Sublabel | Icon | Row | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) | Retention/Purpose |
|----|-------|------|----------|------|-----|-------------|-------------------|------------------|------------------|-------------------|
| **gld-feature** | Features | PRIMARY | ML store | ai-ml | 0 | Feature store for ML models | Azure ML Feature Store | Amazon SageMaker | Vertex AI Feature Store | ML features |
| **gld-lake** | Business | PRIMARY | 7yr retention | data-warehouse | 1 | Business-ready data warehouse | Azure Synapse | Amazon Redshift | BigQuery | 7 years retention |
| **gld-mart** | Marts | PRIMARY | Aggregates | int-bi | 2 | Data marts for specific business domains | Azure Synapse | Amazon Redshift | BigQuery | Business aggregates |

**Notes:**
- Gold = Curated zone - business-ready data models
- All components are PRIMARY as they serve critical business functions
- 7-year data retention for compliance
- Separate stores for BI, ML, and API consumption
- **Vertical alignment:** Features (row 0) aligns with ML Training, Business (row 1) aligns with Real-time, Marts (row 2) aligns with Data APIs

#### Gold Layer Enrichment Pattern

The Gold layer implements a **dual-path enrichment pattern** with both direct Silver→Gold pipelines and internal Gold enrichment:

| Source | Target | Connection Type | Label | Frequency | Purpose |
|--------|--------|----------------|-------|-----------|---------|
| Silver Clean Lake | Business Lake | Data (green) | - | Continuous | Direct business modeling |
| Silver Clean Lake | Features | Data (green) | - | Continuous | Direct feature engineering |
| Silver Clean Lake | Marts | Data (green) | - | Continuous | Direct aggregation |
| Business Lake | Features | Sync (gray) | "Weekly enrichment" | Weekly | Add business context to features |
| Business Lake | Marts | Sync (gray) | "Daily enrichment" | Daily | Add business context to aggregates |

**Enrichment Benefits:**
- **Direct pipelines:** Enable fast, independent data processing for each Gold component
- **Internal enrichment:** Business Lake adds contextual data to Features and Marts
- **Vertical arrows:** Business-to-Features (upward) and Business-to-Marts (downward) connections visualized as straight vertical lines
- **Labels on arrows:** Connection labels positioned directly on the vertical arrow lines for clarity

---

### 3.6 Consumption Layer (4 components)

| ID | Label | Role | Sublabel | Icon | Row | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) | Consumer Type |
|----|-------|------|----------|------|-----|-------------|-------------------|------------------|------------------|---------------|
| **con-ml** | ML Training | PRIMARY | Pipelines | ai-ml | 0 | Machine learning training pipelines | Azure ML | Amazon SageMaker | Vertex AI | Data scientists |
| **con-realtime** | Real-time | PRIMARY | Streaming | ux-dash | 1 | Real-time streaming dashboards | Azure Stream Analytics | Amazon Kinesis Analytics | Cloud Dataflow | Operations team |
| **con-api** | Data APIs | PRIMARY | REST | edge-api | 2 | REST APIs for application integration | Azure API Management | Amazon API Gateway | Cloud API Gateway | Applications |
| **con-bi** | BI Reports | PRIMARY | Dashboards | int-bi | 3 | Business intelligence dashboards and reports | Power BI | Amazon QuickSight | Looker | Business users |

**Notes:**
- All consumption components are PRIMARY as they directly serve end users
- Multiple consumption patterns: ML pipelines, real-time dashboards, APIs, BI reports
- Different user personas: data scientists, operations team, developers, business analysts
- **Vertical alignment:** ML Training (row 0) aligns with Features, Real-time (row 1) aligns with Business, Data APIs (row 2) aligns with Marts
- **Fixed positioning:** Uses `fixedPositioning: true` to maintain explicit row order without dynamic repositioning

---

### 3.7 Governance Layer (4 components - Crosscutting)

| ID | Label | Role | Sublabel | Description | Technology (Azure) | Technology (AWS) | Technology (GCP) | Scope |
|----|-------|------|----------|-------------|-------------------|------------------|------------------|-------|
| **gov-lineage** | Lineage | CROSSCUTTING | Impact | Data lineage tracking and impact analysis | Azure Purview | AWS Glue Lineage | Cloud Data Lineage | All layers |
| **gov-access** | Access | CROSSCUTTING | RBAC | Role-based access control | Azure AD + RBAC | AWS IAM | Cloud IAM | All layers |
| **gov-audit** | Audit | CROSSCUTTING | Logs | Audit logging and compliance | Azure Monitor | AWS CloudTrail | Cloud Audit Logs | All layers |
| **gov-pii** | PII | CROSSCUTTING | Masking | PII detection and masking | Azure Information Protection | AWS Macie | Cloud DLP | All layers |

**Notes:**
- CROSSCUTTING = Applies to all layers simultaneously
- Governance enforced at every stage of data pipeline
- Compliance requirements: data lineage, access control, audit trails, PII protection

---

## 4. Data Flow Scenarios

**Overview:**
This section documents the major data flow patterns in the architecture. The system supports multiple data sources flowing through the medallion architecture (Bronze → Silver → Gold) to various consumption endpoints.

**Flow Scenarios:**
The architecture supports 4 primary flow scenarios (defined in YAML):
- **Real-time Flow** - IoT sensors and cameras to real-time dashboards
- **Batch Flow** - Documents and weather data to BI reports
- **ML Flow** - Gold business data to ML training pipelines
- **CDC Flow** - ERP data to APIs via Change Data Capture

**Connection Styling:**
- **Data connections** (solid green lines, 2px): Primary data flow through the pipeline
- **Sync connections** (dotted gray lines, 1.5px): Supporting synchronization and metadata operations

**Flow Scenarios:**
The sections below document 4 flow scenarios, including how different data sources use the same flow paths:
- 4.1: Real-time Flow - IoT Sensors + Cameras
- 4.2: Batch Flow - Documents + Weather APIs
- 4.3: ML Flow - Feature engineering and model training
- 4.4: CDC Flow - ERP integration via Change Data Capture

---

### 4.0 Complete Connection List (All 22 Connections)

#### Sources → Ingestion Layer (5 connections)

| # | Source | Destination | Type | Protocol | Data Format | Frequency | Volume |
|---|--------|-------------|------|----------|-------------|-----------|--------|
| 1 | **IoT Sensors** | **Streaming Ingestion** | Data | MQTT/AMQP | JSON telemetry | Continuous | 50K devices streaming |
| 2 | **Cameras** | **Streaming Ingestion** | Data | RTSP/WebRTC | Video/Image frames | Continuous | HD video streams |
| 3 | **Documents** | **Batch Ingestion** | Data | File transfer | PDF, DOCX | Daily batches | 10K documents/day |
| 4 | **Weather** | **Batch Ingestion** | Data | REST API | JSON | Hourly | Weather updates |
| 5 | **ERP** | **CDC Ingestion** | Data | Database CDC | Database records | Real-time changes | Transaction logs |

#### Ingestion → Bronze Layer (3 connections)

| # | Source | Destination | Type | Processing | Storage Format | Frequency |
|---|--------|-------------|------|------------|----------------|-----------|
| 6 | **Streaming Ingestion** | **Bronze Raw Lake** | Data | Direct write | Parquet/Avro | Continuous |
| 7 | **Batch Ingestion** | **Bronze Raw Lake** | Data | Bulk load | Parquet | Daily |
| 8 | **CDC Ingestion** | **Bronze Raw Lake** | Data | Log streaming | Parquet | Real-time |

#### Bronze Layer Internal (2 connections)

| # | Source | Destination | Type | Purpose | Trigger | Output |
|---|--------|-------------|------|---------|---------|--------|
| 9 | **Bronze Raw Lake** | **Bronze Schema Registry** | Sync | Schema validation & registration | On data arrival | Schema definitions |
| 10 | **Bronze Raw Lake** | **Bronze Catalog** | Sync | Metadata discovery & indexing | On data arrival | Catalog entries |

#### Bronze → Silver Layer (1 connection)

| # | Source | Destination | Type | Transformation | Quality Checks | Output |
|---|--------|-------------|------|----------------|----------------|--------|
| 11 | **Bronze Raw Lake** | **Silver Clean Lake** | Data | Cleansing, validation, standardization | Data quality rules | Validated data |

#### Silver Layer Internal (2 connections)

| # | Source | Destination | Type | Purpose | Processing | Output |
|---|--------|-------------|------|---------|------------|--------|
| 12 | **Silver Clean Lake** | **Silver Quality Checker** | Sync | Quality validation & scoring | Rule-based validation | Quality metrics |
| 13 | **Silver Clean Lake** | **Silver Deduplicator** | Sync | Entity resolution & dedup | Fuzzy matching | Deduplicated records |

#### Silver → Gold Layer (3 connections)

| # | Source | Destination | Type | Transformation | Modeling | Output |
|---|--------|-------------|------|----------------|----------|--------|
| 14 | **Silver Clean Lake** | **Gold Business Lake** | Data | Business modeling, enrichment | Dimensional modeling | Business entities |
| 15 | **Silver Clean Lake** | **Gold Feature Store** | Data | Feature engineering pipeline | ML feature computation | ML-ready features |
| 16 | **Silver Clean Lake** | **Gold Data Marts** | Data | Direct aggregation pipeline | Business metrics | Analytics-ready marts |

#### Gold Layer Internal (2 connections)

| # | Source | Destination | Type | Purpose | Processing | Output |
|---|--------|-------------|------|---------|------------|--------|
| 17 | **Gold Business Lake** | **Gold Feature Store** | Sync | Additional feature enrichment | Business context features | Enriched features |
| 18 | **Gold Business Lake** | **Gold Data Marts** | Sync | Business context aggregation | Enriched aggregates | Contextualized marts |

#### Gold → Consumption Layer (4 connections)

| # | Source | Destination | Type | Purpose | Refresh | Consumer |
|---|--------|-------------|------|---------|---------|----------|
| 19 | **Gold Business Lake** | **BI Reports** | Sync | Dashboard refresh | Hourly | Business analysts |
| 20 | **Gold Feature Store** | **ML Training** | Data | Model training pipeline | Weekly | Data scientists |
| 21 | **Gold Data Marts** | **Data APIs** | Sync | API backend sync | Real-time | Applications |
| 22 | **Gold Business Lake** | **Real-time Dashboard** | Data | Streaming analytics | Real-time | Operations team |

---

### 4.1 Real-time Flow Scenario

**Use Cases:**
- Live construction site monitoring from IoT sensors
- Real-time video analytics for safety monitoring from cameras

**Data Sources:**
- **IoT Sensors:** 50K devices streaming telemetry (temperature, vibration, pressure)
- **Cameras:** HD video streams for safety violation detection and alerts

| Step | Source | Destination | Connection Type | Processing | Latency | Data Volume |
|------|--------|-------------|-----------------|------------|---------|-------------|
| 1 | IoT Sensors / Cameras | Streaming Ingestion | Data (solid) | Ingest sensor telemetry / video frames | < 1 second | 50K devices + video feeds |
| 2 | Streaming Ingestion | Bronze Raw Lake | Data (solid) | Store raw telemetry / video | < 2 seconds | 10M records/day |
| 3 | Bronze Raw Lake | Silver Clean Lake | Data (solid) | Validate & clean / object detection | < 5 seconds | Filtered stream |
| 4 | Silver Clean Lake | Gold Business Lake | Data (solid) | Apply business rules / safety validation | < 10 seconds | Enriched stream |
| 5 | Gold Business Lake | Real-time Dashboard | Data (solid) | Display live metrics / alerts | < 15 seconds | Dashboard updates |

**Flow Characteristics:**
- **Color Code:** Green (#10b981)
- **Speed:** 1.5x (faster animation)
- **Use Cases:** Live construction monitoring, safety violation detection
- **End-to-end Latency:** < 15 seconds from sensor/camera to dashboard
- **Components Used:** 5 data connections
- **Path:** src-iot/src-camera → ing-stream → brz-lake → slv-lake → gld-lake → con-realtime

---

### 4.2 Batch Flow Scenario

**Use Cases:**
- Daily construction document analysis and reporting
- Hourly weather data ingestion for risk correlation

**Data Sources:**
- **Documents:** 10K PDFs/day (permits, contracts, inspection reports)
- **Weather APIs:** Hourly weather updates (temperature, precipitation, wind)

| Step | Source | Destination | Connection Type | Processing | Frequency | Data Volume |
|------|--------|-------------|-----------------|------------|-----------|-------------|
| 1 | Documents / Weather APIs | Batch Ingestion | Data (solid) | Extract metadata / API pull | Daily / Hourly | 10K docs/day + weather updates |
| 2 | Batch Ingestion | Bronze Raw Lake | Data (solid) | Store raw documents / weather | Daily / Hourly | Full corpus |
| 3 | Bronze Raw Lake | Silver Clean Lake | Data (solid) | Parse & standardize | Daily / Hourly | Validated data |
| 4 | Silver Clean Lake | Gold Business Lake | Data (solid) | Apply data models / join weather | Daily / Hourly | Business entities |
| 5 | Gold Business Lake | Gold Data Marts | Sync (dotted) | Aggregate metrics | Daily | Summary tables |
| 6 | Gold Data Marts | BI Reports | Sync (dotted) | Generate reports | Daily | Dashboards |

**Flow Characteristics:**
- **Color Code:** Purple (#8b5cf6)
- **Speed:** 2.0x (slower, batch process)
- **Use Cases:** Document analysis, weather impact analysis on construction
- **Schedule:** Nightly batch jobs (documents), hourly ingestion (weather)
- **Components Used:** 4 data + 2 sync connections
- **Path:** src-docs/src-weather → ing-batch → brz-lake → slv-lake → gld-lake → gld-mart → con-bi

---

### 4.3 ML Flow Scenario

**Use Case:** Weekly machine learning model training for risk prediction

| Step | Source | Destination | Connection Type | Processing | Purpose | Update Frequency |
|------|--------|-------------|-----------------|------------|---------|------------------|
| 1 | Gold Business Lake | Gold Feature Store | Sync (dotted) | Feature engineering | Create ML features | Continuous |
| 2 | Gold Feature Store | ML Training | Data (solid) | Model training | Train risk models | Weekly |

**Flow Characteristics:**
- **Color Code:** Orange (#f59e0b)
- **Speed:** 1.8x
- **Use Case:** Construction risk prediction models
- **Models:** Risk scoring, anomaly detection, predictive maintenance
- **Components Used:** 1 sync + 1 data connection
- **Path:** gld-lake → gld-feature → con-ml

---

### 4.4 CDC Flow Scenario (ERP Integration)

**Use Case:** Real-time ERP data synchronization to APIs

**Data Source:**
- **ERP Systems:** SAP/Oracle transaction changes via Change Data Capture (CDC)

| Step | Source | Destination | Connection Type | Processing | Latency |
|------|--------|-------------|-----------------|------------|---------|
| 1 | ERP System | CDC Ingestion | Data (solid) | Change data capture | < 1 second |
| 2 | CDC Ingestion | Bronze Raw Lake | Data (solid) | Log persistence | < 2 seconds |
| 3 | Bronze Raw Lake | Silver Clean Lake | Data (solid) | Transform & validate | < 5 seconds |
| 4 | Silver Clean Lake | Gold Business Lake | Data (solid) | Business modeling | < 10 seconds |
| 5 | Gold Business Lake | Gold Data Marts | Sync (dotted) | Aggregate for APIs | < 15 seconds |
| 6 | Gold Data Marts | Data APIs | Sync (dotted) | API backend sync | < 20 seconds |

**Flow Characteristics:**
- **Use Case:** Application integration with ERP systems
- **End-to-end Latency:** < 20 seconds
- **Components Used:** 5 data + 2 sync connections
- **Path:** src-erp → ing-cdc → brz-lake → slv-lake → gld-lake → gld-mart → con-api
- **Note:** CDC enables real-time synchronization of transactional data to application APIs

---
## 5. Connection Types

| Connection Type | Visual Style | Stroke Color | Stroke Width | Pattern | Purpose | Example |
|----------------|--------------|--------------|--------------|---------|---------|---------|
| **Data** | Solid line | Green (#10b981) | 2px | Solid | Primary data flow | Sources → Ingestion → Bronze → Silver → Gold → Consumption |
| **Sync** | Dotted line | Gray (#94a3b8) | 1.5px | Dash: 4,4 | Supporting/synchronization | Bronze Lake → Schema Registry |

**Connection Patterns:**

### Primary Data Connections (14 connections)
- Sources → Ingestion: 5 connections
- Ingestion → Bronze: 3 connections
- Bronze → Silver: 1 connection
- Silver → Gold: 3 connections (business lake, features, marts)
- Gold → Consumption: 2 connections (features → ML, business → realtime)

### Supporting Sync Connections (8 connections)
- Bronze internal: 2 connections (lake → schema, lake → catalog)
- Silver internal: 2 connections (lake → quality, lake → dedup)
- Gold internal: 2 connections (business → features enrichment, business → marts enrichment)
- Gold → Consumption: 2 connections (business → BI, marts → API)

---

## 6. Technology Stack by Cloud Provider

### 6.1 Microsoft Azure Stack

| Layer | Component | Azure Service | Service Category | Pricing Tier Suggestion |
|-------|-----------|---------------|------------------|------------------------|
| Sources | IoT Sensors | Azure IoT Hub | IoT | Standard S1 |
| Sources | Cameras | Azure Computer Vision | AI/ML | Standard S1 |
| Sources | Documents | Azure Blob Storage | Storage | Hot tier |
| Sources | Weather APIs | Azure Logic Apps | Integration | Consumption plan |
| Sources | ERP | Azure SQL Database | Database | General Purpose |
| Ingestion | Streaming | Azure Event Hubs | Messaging | Standard tier |
| Ingestion | Batch | Azure Data Factory | ETL | Pay-per-use |
| Ingestion | CDC | Azure Stream Analytics | Stream processing | Standard |
| Bronze | Raw Lake | Azure Data Lake Gen2 | Storage | Hot tier |
| Bronze | Schema Registry | Azure Schema Registry | Metadata | Standard |
| Bronze | Catalog | Azure Purview | Data governance | Standard |
| Silver | Clean Lake | Azure Data Lake Gen2 | Storage | Hot tier |
| Silver | Quality | Azure Data Quality | Data quality | Standard |
| Silver | Dedup | Azure Databricks | Analytics | Standard cluster |
| Gold | Business | Azure Synapse Analytics | Data warehouse | DW100c |
| Gold | Features | Azure ML Feature Store | ML | Standard |
| Gold | Marts | Azure Synapse Analytics | Data warehouse | DW100c |
| Consumption | BI Reports | Power BI | Business intelligence | Pro |
| Consumption | ML Training | Azure Machine Learning | ML platform | Standard |
| Consumption | Data APIs | Azure API Management | API gateway | Developer |
| Consumption | Real-time | Azure Stream Analytics | Stream processing | Standard |
| Governance | Lineage | Azure Purview | Data governance | Standard |
| Governance | Access | Azure AD + RBAC | Identity | Premium P1 |
| Governance | Audit | Azure Monitor | Monitoring | Pay-per-use |
| Governance | PII | Azure Information Protection | Security | P1 |

---

### 6.2 AWS Stack

| Layer | Component | AWS Service | Service Category | Pricing Tier Suggestion |
|-------|-----------|-------------|------------------|------------------------|
| Sources | IoT Sensors | AWS IoT Core | IoT | Pay-per-use |
| Sources | Cameras | Amazon Rekognition | AI/ML | Pay-per-use |
| Sources | Documents | Amazon S3 | Storage | Standard |
| Sources | Weather APIs | AWS Lambda | Compute | Pay-per-use |
| Sources | ERP | Amazon RDS | Database | db.t3.large |
| Ingestion | Streaming | Amazon Kinesis | Streaming | On-demand |
| Ingestion | Batch | AWS Glue | ETL | Pay-per-use |
| Ingestion | CDC | AWS DMS | Database migration | dms.t3.medium |
| Bronze | Raw Lake | Amazon S3 | Storage | Standard |
| Bronze | Schema Registry | AWS Glue Data Catalog | Metadata | Pay-per-use |
| Bronze | Catalog | AWS Glue Catalog | Data catalog | Pay-per-use |
| Silver | Clean Lake | Amazon S3 | Storage | Standard |
| Silver | Quality | AWS Deequ | Data quality | EMR cluster |
| Silver | Dedup | Amazon EMR | Analytics | m5.xlarge cluster |
| Gold | Business | Amazon Redshift | Data warehouse | dc2.large |
| Gold | Features | Amazon SageMaker | ML | ml.m5.xlarge |
| Gold | Marts | Amazon Redshift | Data warehouse | dc2.large |
| Consumption | BI Reports | Amazon QuickSight | Business intelligence | Enterprise |
| Consumption | ML Training | Amazon SageMaker | ML platform | ml.p3.2xlarge |
| Consumption | Data APIs | Amazon API Gateway | API gateway | Pay-per-use |
| Consumption | Real-time | Amazon Kinesis Analytics | Stream processing | Pay-per-use |
| Governance | Lineage | AWS Glue Lineage | Data governance | Pay-per-use |
| Governance | Access | AWS IAM | Identity | Free |
| Governance | Audit | AWS CloudTrail | Monitoring | Pay-per-use |
| Governance | PII | AWS Macie | Security | Pay-per-use |

---

### 6.3 Google Cloud Platform Stack

| Layer | Component | GCP Service | Service Category | Pricing Tier Suggestion |
|-------|-----------|-------------|------------------|------------------------|
| Sources | IoT Sensors | Cloud IoT Core | IoT | Standard |
| Sources | Cameras | Cloud Vision API | AI/ML | Pay-per-use |
| Sources | Documents | Cloud Storage | Storage | Standard |
| Sources | Weather APIs | Cloud Functions | Compute | Pay-per-use |
| Sources | ERP | Cloud SQL | Database | db-n1-standard-1 |
| Ingestion | Streaming | Cloud Pub/Sub | Messaging | Pay-per-use |
| Ingestion | Batch | Cloud Dataflow | Data processing | Pay-per-use |
| Ingestion | CDC | Cloud Datastream | Change data capture | Pay-per-use |
| Bronze | Raw Lake | Cloud Storage | Storage | Standard |
| Bronze | Schema Registry | Cloud Data Catalog | Metadata | Pay-per-use |
| Bronze | Catalog | Cloud Data Catalog | Data catalog | Pay-per-use |
| Silver | Clean Lake | Cloud Storage | Storage | Standard |
| Silver | Quality | Cloud DQ | Data quality | Pay-per-use |
| Silver | Dedup | Cloud Dataproc | Analytics | n1-standard-4 |
| Gold | Business | BigQuery | Data warehouse | Flat-rate or on-demand |
| Gold | Features | Vertex AI Feature Store | ML | Pay-per-use |
| Gold | Marts | BigQuery | Data warehouse | Flat-rate or on-demand |
| Consumption | BI Reports | Looker | Business intelligence | Looker Standard |
| Consumption | ML Training | Vertex AI | ML platform | n1-standard-4 |
| Consumption | Data APIs | Cloud API Gateway | API gateway | Pay-per-use |
| Consumption | Real-time | Cloud Dataflow | Stream processing | Pay-per-use |
| Governance | Lineage | Cloud Data Lineage | Data governance | Pay-per-use |
| Governance | Access | Cloud IAM | Identity | Free |
| Governance | Audit | Cloud Audit Logs | Monitoring | Pay-per-use |
| Governance | PII | Cloud DLP | Security | Pay-per-use |

---

## 7. Component Roles Classification

| Role Type | Count | Size (W×H) | Opacity | Border Width | Font Size | Icon Size | Purpose | Visual Treatment |
|-----------|-------|------------|---------|--------------|-----------|-----------|---------|------------------|
| **PRIMARY (Core)** | 18 | 140×70px | 100% | 2px | 12px | 30px | Critical path components | Uniform size, prominent |
| **SECONDARY (Supportive)** | 6 | 140×70px | 100% | 2px | 12px | 30px | Supporting services | Uniform size with core |
| **CROSSCUTTING** | 4 | 140×70px | 100% | 2px | 12px | 30px | Cross-layer governance | Uniform size, spans all layers |

**Note:** All component boxes are now uniform in size, opacity, and styling for consistent visual appearance. Role distinction is achieved through positioning rather than size differences.

### Component Distribution by Role:
- **Sources:** 5 Primary
- **Ingestion:** 3 Primary
- **Bronze:** 1 Primary, 2 Secondary
- **Silver:** 1 Primary, 2 Secondary
- **Gold:** 3 Primary
- **Consumption:** 4 Primary
- **Governance:** 4 Crosscutting

---

## 8. Key Metrics and Specifications

| Metric | Value | Description |
|--------|-------|-------------|
| **Data Volume** | 10M records/day | Daily ingestion rate into Bronze layer |
| **Data Quality** | 99.5% | Quality threshold maintained in Silver layer |
| **IoT Devices** | 50,000 | Active sensor devices |
| **Retention Period** | 7 years | Data retention in Gold layer for compliance |
| **Real-time Latency** | < 15 seconds | End-to-end latency for real-time flow |
| **Batch Frequency** | Daily | Batch processing schedule |
| **ML Update Frequency** | Weekly | Model retraining schedule |
| **Document Volume** | 10K/day | Daily document ingestion |
| **Data Flows** | 3 | Real-time, Batch, ML flows |
| **Total Components** | 25 | Complete system components |
| **Cloud Providers** | 3 | Azure, AWS, GCP support |

---

## 9. Architecture Principles

| Principle | Implementation | Benefit |
|-----------|----------------|---------|
| **Medallion Architecture** | Bronze → Silver → Gold | Progressive data refinement, clear quality zones |
| **Separation of Concerns** | Distinct layers for ingestion, storage, processing, consumption | Modular design, easier maintenance |
| **Multi-Cloud Support** | Provider-agnostic design | Avoid vendor lock-in, flexibility |
| **Role-Based Design** | Primary, Secondary, Crosscutting roles | Visual hierarchy, importance indication |
| **Data Quality Gates** | Quality checks at Silver layer | Ensure data reliability |
| **Governance by Design** | Crosscutting governance layer | Compliance, security, auditability |
| **Scalability** | Cloud-native services | Handle growing data volumes |
| **Real-time + Batch** | Hybrid processing | Support diverse use cases |

---

## 10. Diagram Features and Interactivity

| Feature | Implementation | Location/Behavior | Purpose |
|---------|----------------|-------------------|---------|
| **Layer Legend** | Horizontal color-coded boxes | Top-right corner | Quick layer identification |
| **Zoom Controls** | Zoom In/Out/Reset buttons | Top-right header | Navigate large diagrams |
| **Zoom Percentage** | Dynamic percentage display | Top-right header | Current zoom level indicator |
| **Pan/Drag** | Click and drag canvas | Entire diagram | Navigate when zoomed in |
| **Connection Labels** | Inline text annotations | On vertical arrows | Enrichment frequency (Weekly/Daily) |
| **Orthogonal Routing** | Right-angle connection paths | All connections | Clean, organized flow visualization |
| **Vertical Alignment** | Fixed positioning | Gold ↔ Consumption | Clear mapping between layers |

**Layout Specifications:**
- **Node Spacing:** 60px vertical gap between nodes (increased for annotation space)
- **Layer Spacing:** 80px minimum gap between layers (increased from 40px)
- **Supportive Node Spacing:** 25px horizontal gap between supportive nodes
- **Diagram Direction:** Left-to-right data flow

**Connection Routing Patterns:**
- **Horizontal connections:** Standard orthogonal routing (H → V → H)
- **Vertical internal connections:** Straight vertical lines (bidirectional)
- **Core ↔ Supportive:** Vertical routing with arrows pointing down to core

---

## 11. Data Governance Controls

| Control | Type | Coverage | Implementation | Compliance Impact |
|---------|------|----------|----------------|-------------------|
| **Data Lineage** | Tracking | All layers | Track data from source to consumption | Impact analysis, troubleshooting |
| **Access Control** | Security | All components | Role-based access control (RBAC) | Prevent unauthorized access |
| **Audit Logs** | Compliance | All operations | Comprehensive activity logging | Regulatory compliance, forensics |
| **PII Masking** | Privacy | All layers | Automatic PII detection and masking | GDPR, CCPA compliance |

---

## Summary

This Construction Risk Analytics Data Architecture implements a **modern Medallion architecture** with:

- **3-tier data refinement:** Bronze (raw) → Silver (clean) → Gold (curated)
- **25 components** across 7 layers
- **3 cloud providers** supported (Azure, AWS, GCP)
- **3 data processing patterns:** Real-time streaming, Batch processing, ML pipelines
- **4 governance controls:** Lineage, Access, Audit, PII protection
- **Multi-modal data sources:** IoT sensors, cameras, documents, weather, ERP
- **Diverse consumption patterns:** BI reports, ML training, APIs, real-time dashboards

The architecture is designed for **scalability, compliance, and multi-cloud flexibility** while maintaining clear separation of concerns and data quality standards.
# Complete Connection Matrix - INSERT INTO DOCUMENTATION

## 5. Complete Connection Matrix (22 Total Connections)

### 5.1 All Connections Detailed Table

| # | Source Layer | Source Component | Destination Layer | Destination Component | Connection Type | Visual | Purpose | Data Volume/Frequency |
|---|-------------|------------------|-------------------|----------------------|-----------------|--------|---------|---------------------|
| **1** | Sources | IoT Sensors (src-iot) | Ingestion | Streaming (ing-stream) | **Data** | Solid green | Real-time sensor telemetry | 50K devices continuous |
| **2** | Sources | Cameras (src-camera) | Ingestion | Streaming (ing-stream) | **Data** | Solid green | Video/image data streaming | Continuous video feeds |
| **3** | Sources | Documents (src-docs) | Ingestion | Batch (ing-batch) | **Data** | Solid green | Document ingestion | 10K docs/day |
| **4** | Sources | Weather (src-weather) | Ingestion | Batch (ing-batch) | **Data** | Solid green | Weather API data pull | Hourly batches |
| **5** | Sources | ERP (src-erp) | Ingestion | CDC (ing-cdc) | **Data** | Solid green | ERP change data capture | Real-time changes |
| **6** | Ingestion | Streaming (ing-stream) | Bronze | Raw Lake (brz-lake) | **Data** | Solid green | Stream to raw storage | Continuous |
| **7** | Ingestion | Batch (ing-batch) | Bronze | Raw Lake (brz-lake) | **Data** | Solid green | Batch to raw storage | Daily |
| **8** | Ingestion | CDC (ing-cdc) | Bronze | Raw Lake (brz-lake) | **Data** | Solid green | CDC to raw storage | Real-time |
| **9** | Bronze | Raw Lake (brz-lake) | Bronze | Schema (brz-schema) | **Sync** | Dotted gray | Schema validation/registration | As data arrives |
| **10** | Bronze | Raw Lake (brz-lake) | Bronze | Catalog (brz-catalog) | **Sync** | Dotted gray | Metadata cataloging | As data arrives |
| **11** | Bronze | Raw Lake (brz-lake) | Silver | Clean Lake (slv-lake) | **Data** | Solid green | Raw → cleaned transformation | Continuous/scheduled |
| **12** | Silver | Clean Lake (slv-lake) | Silver | Quality (slv-quality) | **Sync** | Dotted gray | Quality validation checks | Continuous |
| **13** | Silver | Clean Lake (slv-lake) | Silver | Dedup (slv-dedup) | **Sync** | Dotted gray | Deduplication processing | Continuous |
| **14** | Silver | Clean Lake (slv-lake) | Gold | Business (gld-lake) | **Data** | Solid green | Clean → business transformation | Scheduled |
| **15** | Silver | Clean Lake (slv-lake) | Gold | Features (gld-feature) | **Data** | Solid green | Direct feature engineering | Continuous |
| **16** | Silver | Clean Lake (slv-lake) | Gold | Marts (gld-mart) | **Data** | Solid green | Direct mart aggregation | Continuous |
| **17** | Gold | Business (gld-lake) | Gold | Features (gld-feature) | **Sync** | Dotted gray | Additional ML feature enrichment | Weekly |
| **18** | Gold | Business (gld-lake) | Gold | Marts (gld-mart) | **Sync** | Dotted gray | Business context aggregation | Daily |
| **19** | Gold | Business (gld-lake) | Consumption | BI Reports (con-bi) | **Sync** | Dotted gray | BI data sync | Hourly refresh |
| **20** | Gold | Features (gld-feature) | Consumption | ML Training (con-ml) | **Data** | Solid green | Feature → training pipeline | Weekly |
| **21** | Gold | Marts (gld-mart) | Consumption | Data APIs (con-api) | **Sync** | Dotted gray | Mart → API backend | Real-time |
| **22** | Gold | Business (gld-lake) | Consumption | Real-time (con-realtime) | **Data** | Solid green | Streaming dashboard data | Real-time |

---

### 5.2 Connections by Type

#### Data Connections (11 total - Main data flow)

| Source | Destination | Flow Path | Purpose |
|--------|------------|-----------|---------|
| IoT Sensors | Streaming Ingestion | Sources → Ingestion | Real-time telemetry |
| Cameras | Streaming Ingestion | Sources → Ingestion | Video/image streams |
| Documents | Batch Ingestion | Sources → Ingestion | Document batches |
| Weather | Batch Ingestion | Sources → Ingestion | Weather API data |
| ERP | CDC Ingestion | Sources → Ingestion | ERP changes |
| Streaming Ingestion | Bronze Raw Lake | Ingestion → Bronze | Stream persistence |
| Batch Ingestion | Bronze Raw Lake | Ingestion → Bronze | Batch persistence |
| CDC Ingestion | Bronze Raw Lake | Ingestion → Bronze | CDC persistence |
| Bronze Raw Lake | Silver Clean Lake | Bronze → Silver | Cleaning/validation |
| Silver Clean Lake | Gold Business | Silver → Gold | Business modeling |
| Gold Features | ML Training | Gold → Consumption | ML consumption |
| Gold Business | Real-time Dashboard | Gold → Consumption | Dashboard consumption |

| Silver Clean Lake | Gold Features | Silver → Gold | Feature pipeline |
| Silver Clean Lake | Gold Marts | Silver → Gold | Analytics pipeline |

**Total: 14 data connections** (solid green lines)

#### Sync Connections (8 total - Supporting operations)

| Source | Destination | Purpose | Frequency |
|--------|------------|---------|-----------|
| Bronze Raw Lake | Bronze Schema | Schema registration | Continuous |
| Bronze Raw Lake | Bronze Catalog | Metadata cataloging | Continuous |
| Silver Clean Lake | Silver Quality | Quality validation | Continuous |
| Silver Clean Lake | Silver Dedup | Deduplication | Continuous |
| Gold Business | Gold Features | Feature enrichment | Weekly |
| Gold Business | Gold Marts | Mart enrichment | Daily |
| Gold Business | BI Reports | BI data refresh | Hourly |
| Gold Marts | Data APIs | API backend sync | Real-time |

**Total: 8 sync connections** (dotted gray lines)

**TOTAL: 22 connections**

---

### 5.3 Connection Matrix by Layer

#### Sources → Ingestion (5 connections)

| Source | Ingestion Target | Type | Data Volume |
|--------|-----------------|------|-------------|
| IoT Sensors | Streaming | Data | 50K devices |
| Cameras | Streaming | Data | Video feeds |
| Documents | Batch | Data | 10K/day |
| Weather | Batch | Data | Hourly |
| ERP | CDC | Data | Real-time changes |

#### Ingestion → Bronze (3 connections)

| Ingestion Source | Bronze Target | Type | Pattern |
|-----------------|---------------|------|---------|
| Streaming | Raw Lake | Data | Continuous |
| Batch | Raw Lake | Data | Scheduled |
| CDC | Raw Lake | Data | Event-driven |

#### Bronze Internal (2 connections)

| Source | Target | Type | Purpose |
|--------|--------|------|---------|
| Raw Lake | Schema Registry | Sync | Schema validation |
| Raw Lake | Catalog | Sync | Metadata discovery |

#### Bronze → Silver (1 connection)

| Source | Target | Type | Transformation |
|--------|--------|------|---------------|
| Raw Lake | Clean Lake | Data | Validation, cleaning, dedup |

#### Silver Internal (2 connections)

| Source | Target | Type | Purpose |
|--------|--------|------|---------|
| Clean Lake | Quality Checker | Sync | Quality rules |
| Clean Lake | Deduplicator | Sync | Entity resolution |

#### Silver → Gold (3 connections)

| Source | Target | Type | Transformation |
|--------|--------|------|---------------|
| Clean Lake | Business Lake | Data | Business modeling, enrichment |
| Clean Lake | Feature Store | Data | Feature engineering pipeline |
| Clean Lake | Data Marts | Data | Direct aggregation pipeline |

#### Gold Internal (2 connections)

| Source | Target | Type | Purpose |
|--------|--------|------|---------|
| Business Lake | Feature Store | Sync | Feature enrichment with business context |
| Business Lake | Data Marts | Sync | Mart enrichment with business context |

#### Gold → Consumption (4 connections)

| Source | Target | Type | Consumer |
|--------|--------|------|----------|
| Business Lake | BI Reports | Sync | Business analysts |
| Feature Store | ML Training | Data | Data scientists |
| Data Marts | Data APIs | Sync | Applications |
| Business Lake | Real-time Dashboard | Data | Operations team |

---

### 5.4 Data Lineage - Complete Paths

#### Path 1: IoT → Real-time Dashboard (6 hops)
```
IoT Sensors → Streaming → Bronze Raw Lake → Silver Clean Lake → Gold Business → Real-time Dashboard
```
- **Connections:** 5 data connections
- **Latency:** < 15 seconds end-to-end
- **Use Case:** Live construction monitoring

#### Path 2: Documents → BI Reports (7 hops)
```
Documents → Batch → Bronze Raw Lake → Silver Clean Lake → Gold Business → Gold Marts → BI Reports
```
- **Connections:** 5 data + 1 sync
- **Frequency:** Daily refresh
- **Use Case:** Daily construction reports

#### Path 3: ERP → API (6 hops)
```
ERP → CDC → Bronze Raw Lake → Silver Clean Lake → Gold Business → Gold Marts → Data APIs
```
- **Connections:** 5 data + 1 sync
- **Latency:** Near real-time
- **Use Case:** Application integration

#### Path 4: All Sources → ML Training (8 hops)
```
All Sources → Ingestion → Bronze → Silver → Gold Business → Gold Features → ML Training
```
- **Connections:** 6 data + 1 sync
- **Frequency:** Weekly model training
- **Use Case:** Risk prediction models

---

### 5.5 Connection Governance

All 22 connections are monitored by the **Governance Layer**:

| Governance Component | Applied To | Purpose |
|---------------------|-----------|---------|
| **Lineage Tracking** | All 22 connections | Track data movement, impact analysis |
| **Access Control** | All data connections (14) | RBAC enforcement on data flows |
| **Audit Logging** | All 22 connections | Log all data transfers |
| **PII Masking** | All connections with PII data | Auto-detect and mask sensitive data |

---

## Summary Statistics

| Metric | Count |
|--------|-------|
| **Total Connections** | 22 |
| **Data Connections (solid green)** | 14 |
| **Sync Connections (dotted gray)** | 8 |
| **Longest Path** | 8 hops (Source → ML) |
| **Shortest Path** | 5 hops (Source → Real-time) |
| **Cross-layer Connections** | 9 |
| **Intra-layer Connections** | 11 |
| **Critical Path Connections** | 6 (Source → Silver → Gold → Consumption) |

---

## Architecture Summary

This **Construction Risk Analytics - Data Architecture** implements a comprehensive Medallion pattern with the following key characteristics:

### Core Design Principles
- **25 total components** across 7 layers (6 horizontal + 1 crosscutting)
- **22 connections** (14 data, 8 sync) with uniform color coding
- **3 data processing patterns:** Real-time streaming, Batch processing, ML pipelines
- **4 governance controls:** Lineage, Access, Audit, PII protection
- **Multi-modal data sources:** IoT sensors, cameras, documents, weather, ERP
- **Diverse consumption patterns:** BI reports, ML training, APIs, real-time dashboards

### Visual Design Features
- **Uniform component sizing:** All boxes are 140×70px regardless of role
- **Vertical alignment:** Gold layer components directly align with their Consumption layer counterparts
- **Orthogonal routing:** Clean right-angle connection paths
- **Dual-path enrichment:** Direct Silver→Gold pipelines + internal Gold layer enrichment
- **Connection labels:** Inline annotations on vertical arrows ("Weekly enrichment", "Daily enrichment")
- **Top-right legend:** Layer identification moved out of main flow
- **Interactive features:** Zoom in/out, pan, drag-to-navigate

### Layer Alignment
| Gold Layer (Row) | Consumption Layer (Row) | Connection Type | Purpose |
|------------------|------------------------|----------------|---------|
| Features (0) | ML Training (0) | Data | ML model training |
| Business (1) | Real-time (1) | Data | Streaming analytics |
| Marts (2) | Data APIs (2) | Sync | Application integration |
| - | BI Reports (3) | Sync | Business intelligence |

### Technology Flexibility
The architecture is designed for **scalability, compliance, and multi-cloud flexibility** while maintaining clear separation of concerns and data quality standards. All components can be implemented using Azure, AWS, or GCP services.
