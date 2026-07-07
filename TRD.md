# ⚙️ Technical Requirements Document (TRD)
## PromptWar — GenAI Smart Stadium Orchestration Platform
### FIFA World Cup 2026 · Google for Developers Challenge

**Document Version:** 1.0  
**Date:** July 2026  
**Status:** Draft — Pending Architecture Review  
**Owner:** Solution Architecture Team  
**Classification:** Internal

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Architecture Design](#2-architecture-design)
3. [Infrastructure Requirements](#3-infrastructure-requirements)
4. [AI/ML Technical Specifications](#4-aiml-technical-specifications)
5. [Data Architecture](#5-data-architecture)
6. [API Design](#6-api-design)
7. [Security Architecture](#7-security-architecture)
8. [Scalability & Performance](#8-scalability--performance)
9. [Integration Requirements](#9-integration-requirements)
10. [DevOps & MLOps](#10-devops--mlops)
11. [Compliance & Regulatory Requirements](#11-compliance--regulatory-requirements)
12. [Technology Stack Summary](#12-technology-stack-summary)

---

## 1. System Overview

### 1.1 System Context

```mermaid
C4Context
    title System Context Diagram — PromptWar Platform

    Person(fan, "Fan", "Match attendee using mobile app for navigation, info, and accessibility")
    Person(ops, "Venue Ops Manager", "Staff managing stadium operations via dashboard")
    Person(security, "Security Officer", "Police/security using real-time crowd and incident tools")
    Person(volunteer, "Volunteer/Usher", "Event volunteer using simplified AI assistant")

    System(promptwar, "PromptWar Platform", "GenAI-enabled smart stadium orchestration system with edge AI, LLM copilot, and real-time analytics")

    System_Ext(fifa, "FIFA Systems", "Tournament data, player tracking, schedule APIs")
    System_Ext(ticketing, "Venue Ticketing", "Seat assignments, access control, fan profiles")
    System_Ext(transport, "Transport APIs", "Public transit, Uber/Lyft, parking systems")
    System_Ext(cctv, "Stadium CCTV", "Camera feeds for computer vision processing")
    System_Ext(weather, "Weather Services", "NOAA, Environment Canada, SMN Mexico")
    System_Ext(llm_api, "LLM APIs", "OpenAI, Anthropic, Google AI Studio")

    Rel(fan, promptwar, "Uses", "HTTPS / WebSocket")
    Rel(ops, promptwar, "Uses", "HTTPS / WebSocket")
    Rel(security, promptwar, "Uses", "HTTPS / Radio API")
    Rel(volunteer, promptwar, "Uses", "HTTPS / Voice")
    Rel(promptwar, fifa, "Queries", "REST API")
    Rel(promptwar, ticketing, "Integrates", "REST API")
    Rel(promptwar, transport, "Queries", "REST API")
    Rel(promptwar, cctv, "Streams", "RTSP / SMPTE ST 2110")
    Rel(promptwar, weather, "Queries", "REST API")
    Rel(promptwar, llm_api, "Calls", "REST API")
```

### 1.2 Architecture Principles

| Principle | Implementation |
|-----------|---------------|
| **Edge-First** | CV, AR, STT/TTS at stadium edge (<50ms); LLM queries at regional/cloud |
| **Multi-Cloud Resilience** | AWS (US), Azure (Canada), GCP (Mexico) with cross-region failover |
| **Data Sovereignty** | Fan data processed and stored within their country; no cross-border PII |
| **Offline-First Mobile** | Cached maps, offline AI models, graceful degradation |
| **Modular Microservices** | Each AI feature is an independent service with clear API contracts |
| **Human-in-the-Loop** | All safety-critical AI outputs require human review/approval |
| **Privacy by Design** | No facial recognition; aggregate analytics; explicit consent |

---

## 2. Architecture Design

### 2.1 Layered Architecture

```mermaid
graph TB
    subgraph L1["Layer 1: Presentation"]
        A1["Fan Mobile App\nReact Native"]
        A2["Ops Dashboard\nNext.js"]
        A3["Volunteer App\nReact Native"]
        A4["Security Console\nNext.js"]
    end

    subgraph L2["Layer 2: API Gateway"]
        B1["Kong API Gateway\nAuth · Rate Limit · Routing"]
        B2["CDN\nCloudFront / Azure CDN"]
        B3["WebSocket Server\nReal-time Events"]
    end

    subgraph L3["Layer 3: Microservices"]
        C1["Copilot Service\nLangChain + LLM"]
        C2["Navigation Service\nRAG + Mapbox"]
        C3["Crowd Analytics\nCV + YOLOv8"]
        C4["Translation Service\nWhisper + GPT"]
        C5["Transport Service\nPredictive ML"]
        C6["Sustainability Service\nAnalytical AI"]
        C7["Notification Service\nPush + SMS"]
        C8["Auth Service\nJWT + OAuth2"]
    end

    subgraph L4["Layer 4: AI Tier"]
        D1["Edge AI\nNVIDIA Jetson / Edge TPU\n< 50ms"]
        D2["Regional LLM\nSelf-hosted Llama/Mistral\n< 200ms"]
        D3["Cloud LLM\nGPT-4o / Gemini\n< 500ms"]
    end

    subgraph L5["Layer 5: Data Platform"]
        E1["Kafka / Kinesis\nStream Processing"]
        E2["Apache Flink\nReal-time Analytics"]
        E3["Data Lake\nS3 / Azure Blob"]
        E4["Vector DB\nPinecone / Weaviate"]
        E5["PostgreSQL\nOperational DB"]
        E6["Redis\nCache / Sessions"]
    end

    subgraph L6["Layer 6: Data Sources"]
        F1["IoT Sensors\nBeacons, People Counters"]
        F2["CCTV Cameras\n45/stadium via RTSP"]
        F3["External APIs\nFIFA, Transport, Weather"]
        F4["POS Systems\nConcessions Data"]
    end

    L1 --> L2
    L2 --> L3
    L3 --> L4
    L3 --> L5
    L4 --> L5
    L5 --> L6
```

### 2.2 Edge AI Architecture (Per Stadium)

```mermaid
graph LR
    subgraph EDGE["Stadium Edge Node - NVIDIA Jetson Orin 64GB"]
        CAM["CCTV Feeds\n45 cameras"] --> INGEST["RTSP Ingest\n+ Frame Sampling"]
        INGEST --> CV["YOLOv8\nCrowd Detection"]
        INGEST --> CV2["Crowd Counter\nDensity Heatmap"]

        MIC["Voice Input\nRadio / Mobile"] --> STT["Whisper v3\nSTT Engine"]
        STT --> LLM_LOCAL["Llama 3 8B Instruct\nLocal LLM"]

        CV --> ALERT["Alert Engine\n< 50ms"]
        CV2 --> ALERT
        LLM_LOCAL --> RESP["Response\nCache"]

        ALERT --> PUSH["Event Stream\nto Cloud"]
        RESP --> PUSH
    end

    PUSH --> CLOUD["Regional / Cloud AI\nfor Complex Queries"]
```

### 2.3 Multi-Region Deployment

```mermaid
graph TB
    subgraph US["United States — AWS"]
        US_EDGE["11 Stadium Edge Nodes"]
        US_REGIONAL["AWS EKS Cluster\nus-east-1 + us-west-2"]
        US_DB["RDS PostgreSQL\n+ S3 Data Lake"]
        US_EDGE --> US_REGIONAL
        US_REGIONAL --> US_DB
    end

    subgraph CA["Canada — Azure"]
        CA_EDGE["2 Stadium Edge Nodes"]
        CA_REGIONAL["Azure AKS\nCanada Central"]
        CA_DB["Azure SQL + Blob"]
        CA_EDGE --> CA_REGIONAL
        CA_REGIONAL --> CA_DB
    end

    subgraph MX["Mexico — GCP"]
        MX_EDGE["3 Stadium Edge Nodes"]
        MX_REGIONAL["GKE Cluster\nnorthamerica-northeast1"]
        MX_DB["Cloud SQL + GCS"]
        MX_EDGE --> MX_REGIONAL
        MX_REGIONAL --> MX_DB
    end

    subgraph IBC["Dallas IBC — Multi-cloud"]
        GLOBAL_LLM["Global LLM Gateway\nOpenAI + Gemini"]
        ANALYTICS["Global Analytics\nBigQuery / Snowflake"]
        OPS_CENTER["24/7 NOC Dashboard"]
    end

    US_REGIONAL <-->|"Data Sync\nAnonymized Only"| IBC
    CA_REGIONAL <-->|"Data Sync\nAnonymized Only"| IBC
    MX_REGIONAL <-->|"Data Sync\nAnonymized Only"| IBC
```

---

## 3. Infrastructure Requirements

### 3.1 Edge Computing (Per Stadium)

| Component | Specification | Count per Stadium |
|-----------|--------------|-------------------|
| Edge AI Server | NVIDIA Jetson Orin 64GB or equivalent | 2 (primary + hot standby) |
| GPU Inference | NVIDIA A100 or RTX 4090 workstation | 1 |
| Local Storage | NVMe SSD 8TB RAID-1 | 2 |
| Network | Dual 10Gbps NICs connected to FIFA 600Gbps | 2 |
| UPS Backup | 4-hour battery backup | 1 |
| OS | Ubuntu 22.04 LTS + CUDA 12.x | — |

### 3.2 Cloud Infrastructure (Per Region)

```mermaid
graph LR
    subgraph K8S["Kubernetes Cluster"]
        NG1["Copilot Pods\nx5 replicas"]
        NG2["CV Analytics Pods\nx3 replicas"]
        NG3["Translation Pods\nx5 replicas"]
        NG4["Navigation Pods\nx3 replicas"]
        NG5["Gateway Pods\nx3 replicas"]
    end

    subgraph DATA["Data Services"]
        KAFKA["Kafka Cluster\n3 brokers"]
        FLINK["Flink Jobs\nStream Processing"]
        VECTOR["Pinecone\nVector DB"]
        CACHE["Redis Cluster\n3 nodes"]
        PG["PostgreSQL\nHA Primary + 2 Replicas"]
    end

    K8S --> DATA
```

### 3.3 Network Requirements

| Link | Specification | Purpose |
|------|------------|---------|
| Stadium → Cloud | 600Gbps (FIFA-provided) | Primary data path |
| Cloud Region ↔ IBC | 10Gbps dedicated | Cross-region sync |
| Edge Node → Cloud | 10Gbps | Real-time streaming |
| Fan Mobile | 5G / Stadium WiFi | App connectivity |
| Staff Devices | Private LTE | Mission-critical communications |

---

## 4. AI/ML Technical Specifications

### 4.1 Model Selection Matrix

```mermaid
graph TD
    subgraph LLM["Large Language Models"]
        L1["GPT-4o / Gemini 1.5 Pro\nCloud LLM\nGeneral chat, complex ops queries\nLatency: < 500ms"]
        L2["Llama 3 70B Instruct\nRegional Self-hosted\nSensitive ops, data sovereignty\nLatency: < 200ms"]
        L3["Llama 3 8B Instruct\nEdge LLM\nVoice commands, simple queries\nLatency: < 100ms"]
    end
    subgraph CV["Computer Vision"]
        CV1["YOLOv8x\nCrowd Detection & Counting\nEdge GPU\nFPS: 30+"]
        CV2["Custom Heatmap CNN\nCrowd Density Visualization\nEdge GPU\nLatency: < 50ms"]
    end
    subgraph SPEECH["Speech AI"]
        S1["Whisper v3 Large\nSTT - Edge or Cloud\nWER < 5% across 50 langs"]
        S2["Azure TTS / ElevenLabs\nTTS for announcements\n100+ language voices"]
    end
    subgraph PREDICT["Predictive Models"]
        P1["LSTM + Prophet\nCrowd Arrival Forecasting\nHorizon: 15-30min"]
        P2["ARIMA + XGBoost\nConcession Demand Prediction\nHorizon: 1-2hr"]
        P3["Custom RL Agent\nShuffle Route Optimization\nReal-time"]
    end
```

### 4.2 RAG (Retrieval-Augmented Generation) Architecture

```mermaid
flowchart LR
    USER["User Query\n'Where is nearest\nwheelchair ramp?'"]

    subgraph RAG["RAG Pipeline"]
        EMBED["Embedding Model\ntext-embedding-3-large\nor Gemini Embeddings"]
        VSTORE["Vector Store\nPinecone / Weaviate\nper-venue shards"]
        RETRIEVE["Top-K Retrieval\nk=5, similarity > 0.75"]
        CONTEXT["Context Assembly\nRetrieved chunks +\nuser query + system prompt"]
        LLM["LLM Generation\nGrounded response with\nvenue-specific facts"]
    end

    subgraph KB["Knowledge Bases"]
        KB1["Venue Maps & Floor Plans"]
        KB2["Operations Manuals"]
        KB3["Tournament Schedule"]
        KB4["Accessibility Routes"]
        KB5["Incident History"]
    end

    USER --> EMBED
    EMBED --> VSTORE
    KB1 & KB2 & KB3 & KB4 & KB5 --> VSTORE
    VSTORE --> RETRIEVE
    RETRIEVE --> CONTEXT
    CONTEXT --> LLM
    LLM --> ANSWER["Grounded Answer\n'The nearest wheelchair ramp\nis 40m to your left at Gate C2'"]
```

### 4.3 Model Fine-tuning Pipeline

```mermaid
flowchart TD
    RAW["Raw Training Data\n- Stadium ops logs\n- Historical incidents\n- Multilingual fan queries\n- Venue FAQs"] --> CLEAN["Data Cleaning\n& Annotation\nHuman + AI-assisted"]
    CLEAN --> SPLIT["Train / Val / Test Split\n80/10/10"]
    SPLIT --> FINETUNE["Fine-tuning\nLoRA on Llama 3 70B\n4-bit quantization"]
    FINETUNE --> EVAL["Evaluation\n- Accuracy on held-out set\n- Bias testing (50 langs)\n- Safety filter testing\n- Latency benchmarks"]
    EVAL -->|"Pass"| DEPLOY["Model Registry\nMLflow\nVersioned & signed"]
    EVAL -->|"Fail"| FINETUNE
    DEPLOY --> SERVE["Serving\nvLLM / TGI\nA/B testing enabled"]
    SERVE --> MONITOR["Production Monitoring\n- Hallucination rate\n- User satisfaction\n- Latency\n- Bias drift"]
    MONITOR -->|"Drift detected"| FINETUNE
```

### 4.4 Computer Vision Pipeline

```mermaid
flowchart LR
    CAMERAS["45 CCTV Cameras\nper stadium\nRTSP streams"] --> INGEST["Frame Sampling\n5 FPS per camera\nfor crowd analysis"]
    INGEST --> PREPROCESS["Preprocessing\nResize + Normalize\n640x640 for YOLO"]
    PREPROCESS --> YOLO["YOLOv8x Inference\nEdge GPU\nPerson detection"]
    YOLO --> DENSITY["Crowd Density\nComputation\nPer zone heatmap"]
    DENSITY --> COMPARE["Threshold Check\nvs. Capacity Limits"]
    COMPARE -->|"Critical zone"| ALERT["Real-time Alert\n< 50ms\nto ops dashboard"]
    COMPARE -->|"Normal"| STORE["Time-series Store\nInfluxDB\nHistorical trends"]
    STORE --> FORECAST["LSTM Forecasting\n15-min lookahead\ncrowd predictions"]
```

---

## 5. Data Architecture

### 5.1 Data Flow

```mermaid
flowchart TD
    subgraph INGEST["Ingestion Layer"]
        I1["IoT Sensors\n1M events/sec"]
        I2["CCTV Cameras\n45 streams/stadium"]
        I3["Mobile App Events\n100K users/stadium"]
        I4["External APIs\nFIFA, Transport, Weather"]
        I5["Voice Input\nStaff/Volunteer"]
    end

    subgraph STREAM["Stream Processing"]
        KAFKA["Apache Kafka\n3-broker cluster\nper region"]
        FLINK["Apache Flink\nReal-time joins\n& aggregations"]
    end

    subgraph STORE["Storage Layer"]
        HOT["Redis Cache\nLatest state\n< 1min TTL"]
        WARM["PostgreSQL\nOperational data\n30-day window"]
        COLD["S3 / Azure Blob\nData Lake\nFull history"]
        VECTOR["Pinecone\nVector embeddings\nRAG knowledge"]
        TSDB["InfluxDB\nTime-series metrics\n& IoT data"]
    end

    subgraph SERVE["Serving Layer"]
        FEATURE["Feature Store\nFeast\nReal-time + batch"]
        WAREHOUSE["BigQuery / Snowflake\nAnalytics & reporting"]
        LLM_CTX["LLM Context\nRAG-assembled\nper query"]
    end

    INGEST --> KAFKA
    KAFKA --> FLINK
    FLINK --> HOT
    FLINK --> WARM
    FLINK --> TSDB
    WARM --> COLD
    HOT & WARM & TSDB --> FEATURE
    COLD --> WAREHOUSE
    VECTOR --> LLM_CTX
    FEATURE --> LLM_CTX
```

### 5.2 Data Schema — Key Entities

#### Crowd Event (IoT → Kafka topic: `crowd.events`)

```json
{
  "event_id": "uuid-v4",
  "timestamp": "ISO-8601",
  "venue_id": "ATT-DALLAS",
  "zone_id": "SECTION-114",
  "event_type": "DENSITY_UPDATE | THRESHOLD_BREACH | INCIDENT",
  "density": 0.78,
  "count_estimate": 342,
  "confidence": 0.94,
  "source": "CV_CAMERA | IOT_BEACON",
  "camera_ids": ["CAM-114-A", "CAM-114-B"],
  "metadata": {}
}
```

#### Fan Session (Mobile → Kafka topic: `fan.sessions`)

```json
{
  "session_id": "uuid-v4",
  "user_id": "hashed-anonymized-id",
  "timestamp": "ISO-8601",
  "venue_id": "ATT-DALLAS",
  "language": "pt-BR",
  "event_type": "NAVIGATION_REQUEST | CHAT_QUERY | AR_SESSION",
  "query": "optional - natural language",
  "location": { "lat": null, "lon": null, "zone": "GATE-C" },
  "accessibility_profile": "WHEELCHAIR | VISUAL | AUDIO | NONE",
  "device_type": "iOS | ANDROID",
  "app_version": "2.1.0"
}
```

### 5.3 Data Privacy Architecture

```mermaid
flowchart TD
    subgraph FAN["Fan Data Flow"]
        FANd["Fan PII\n(location, language,\naccesibility profile)"] --> EDGE_PROC["Edge Processing\n(on-device or stadium edge)\nNo PII leaves device"]
        EDGE_PROC --> ANON["Anonymization Layer\nHashing + aggregation\nbefore cloud upload"]
        ANON --> CLOUD_STORE["Cloud Storage\n(anonymized only)\nIn-country residency"]
    end

    subgraph LEGAL["Legal Framework Overlay"]
        US_LAW["CCPA / CPRA\nCalifornia + US States\nRight to deletion"]
        CA_LAW["PIPEDA + Provincial\nCanada\nExplicit consent required"]
        MX_LAW["LFPDPPP\nMexico\nSensitive data restrictions"]
    end

    CLOUD_STORE --> US_LAW
    CLOUD_STORE --> CA_LAW
    CLOUD_STORE --> MX_LAW
```

---

## 6. API Design

### 6.1 REST API Structure

| Endpoint Group | Base Path | Auth | Rate Limit |
|---------------|-----------|------|-----------|
| Fan Chat | `/api/v1/chat` | JWT Bearer | 60 req/min/user |
| Navigation | `/api/v1/navigation` | JWT Bearer | 120 req/min/user |
| Crowd Status | `/api/v1/crowd` | Service Token | 300 req/min/service |
| Operations Copilot | `/api/v1/ops/query` | Staff JWT | 120 req/min/user |
| Incident Reporting | `/api/v1/ops/incidents` | Staff JWT | 30 req/min/user |
| Sustainability | `/api/v1/sustainability` | Service Token | 60 req/min |
| Transport | `/api/v1/transport` | JWT Bearer | 60 req/min/user |

### 6.2 Key API Contracts

#### Chat API — POST `/api/v1/chat`

```yaml
Request:
  session_id: string (uuid)
  message: string (max 2000 chars)
  language: string (BCP-47, e.g. pt-BR)
  venue_id: string
  context:
    accessibility_profile: enum [WHEELCHAIR, VISUAL, AUDIO, NONE]
    current_zone: string (optional)
    query_type: enum [NAVIGATION, INFO, ACCESSIBILITY, GENERAL]

Response:
  response_id: string (uuid)
  message: string
  language: string
  confidence: float (0.0-1.0)
  sources: array[string]  # RAG source references
  latency_ms: integer
  suggested_actions:
    - type: enum [NAVIGATE, CALL_STAFF, VIEW_MAP]
      payload: object
```

#### Crowd Status — GET `/api/v1/crowd/zones/{venue_id}`

```yaml
Response:
  venue_id: string
  timestamp: ISO-8601
  zones:
    - zone_id: string
      name: string
      capacity: integer
      current_count: integer
      density: float (0.0-1.0)
      status: enum [NORMAL, WARNING, CRITICAL, EVACUATION]
      trend: enum [INCREASING, STABLE, DECREASING]
  overall_status: enum [NORMAL, ELEVATED, CRITICAL]
```

### 6.3 WebSocket Events (Real-time)

```mermaid
sequenceDiagram
    participant C as Client App
    participant WS as WebSocket Server
    participant CV as CV Analytics
    participant ALERT as Alert Engine

    C->>WS: connect(venue_id, zone_subscriptions)
    WS-->>C: connected(session_id)

    loop Every 5 seconds
        CV->>ALERT: density_update(zone_id, density, count)
        ALERT->>WS: broadcast_if_threshold_breach()
        WS-->>C: crowd.update {zone_id, density, status}
    end

    Note over CV,ALERT: On CRITICAL threshold breach
    CV->>ALERT: CRITICAL_BREACH(zone_id, density=0.95)
    ALERT->>WS: PRIORITY broadcast
    WS-->>C: crowd.alert {zone_id, severity=CRITICAL, recommended_action}
```

---

## 7. Security Architecture

### 7.1 Zero-Trust Security Model

```mermaid
graph TB
    subgraph INTERNET["Internet / Public"]
        USER_REQ["User Requests\nFan, Staff, Volunteer"]
    end

    subgraph DMZ["DMZ Layer"]
        WAF["Web Application Firewall\nAWS Shield / Azure DDoS"]
        RATE["Rate Limiter\nKong Gateway"]
    end

    subgraph AUTH["Auth Layer"]
        IDENTITY["Identity Provider\nCognito / Azure AD B2C"]
        JWT["JWT Validation\nRS256 signed"]
        MFA["MFA for Staff\nTOTP / SMS"]
    end

    subgraph SERVICES["Service Mesh"]
        MTLS["mTLS Between Services\nIstio Service Mesh"]
        RBAC["RBAC Policies\nRole-based access"]
    end

    subgraph DATA["Data Layer"]
        ENCRYPT_TRANSIT["TLS 1.3\nIn Transit"]
        ENCRYPT_REST["AES-256\nAt Rest"]
        KMS["Key Management\nAWS KMS / Azure Key Vault"]
    end

    USER_REQ --> WAF --> RATE --> IDENTITY
    IDENTITY --> JWT --> MFA
    MFA --> MTLS --> RBAC
    RBAC --> ENCRYPT_TRANSIT --> ENCRYPT_REST
    ENCRYPT_REST --> KMS
```

### 7.2 Secret Management

| Secret Type | Storage | Rotation |
|------------|---------|---------|
| LLM API Keys | AWS Secrets Manager / Azure Key Vault | Monthly |
| Database Credentials | HashiCorp Vault | Weekly |
| JWT Signing Keys | KMS (HSM-backed) | 90 days |
| Service-to-Service Tokens | Istio mTLS certs | 24 hours |
| Encryption Keys | KMS | Annually |

### 7.3 Security Compliance

| Standard | Requirement | Status |
|----------|-------------|--------|
| SOC 2 Type II | Cloud infrastructure audit | Required before G3 gate |
| OWASP Top 10 | All APIs and web interfaces | Continuous scanning |
| NIST CSF | Cybersecurity framework alignment | Architecture review |
| PCI DSS | If handling payment (concessions) | Scope decision needed |
| Pen Testing | Full external test | Month 8 (pre-G3) |

---

## 8. Scalability & Performance

### 8.1 Load Model

```mermaid
xychart-beta
    title "Estimated API Load — Match Day Timeline"
    x-axis ["T-4hr", "T-2hr", "T-1hr", "T-30min", "Kickoff", "Goal", "Halftime", "FT"]
    y-axis "Requests per Minute (thousands)" 0 --> 500
    bar [20, 80, 150, 280, 450, 500, 200, 300]
    line [20, 80, 150, 280, 450, 500, 200, 300]
```

### 8.2 Auto-scaling Configuration

| Service | Min Pods | Max Pods | Scale Trigger |
|---------|---------|---------|--------------|
| Copilot / LLM | 5 | 50 | CPU > 60% or queue depth > 100 |
| CV Analytics | 3 | 20 | GPU utilization > 70% |
| Translation | 5 | 40 | Request rate > 1000 RPM |
| Navigation | 3 | 30 | CPU > 60% |
| WebSocket | 3 | 20 | Active connections > 10,000 |
| API Gateway | 3 | 15 | CPU > 50% |

### 8.3 Caching Strategy

```mermaid
flowchart LR
    REQ["Incoming Request"] --> CDN["CDN Cache\nStatic assets\nMap tiles\nTTL: 1hr"]
    CDN -->|"Miss"| REDIS["Redis Cache\nLLM responses\nCrowd status\nTTL: 30s-5min"]
    REDIS -->|"Miss"| APP["Application\nService"]
    APP --> LLM["LLM API\n(Expensive)"]
    APP --> DB["Database\n(Fast)"]
    LLM --> REDIS
    DB --> REDIS
```

---

## 9. Integration Requirements

### 9.1 FIFA Technology Integrations

| FIFA System | Integration Type | Data | Protocol |
|------------|-----------------|------|---------|
| Player Tracking | Push / Webhook | 29 points/player @ 50Hz | HTTPS WebSocket |
| Connected Ball (Adidas Trionda) | Push | 500Hz motion sensor | HTTPS |
| SAOT (Semi-automated offside) | Read API | Offside decisions | REST API |
| Match Schedule API | Pull | Fixtures, results, updates | REST API |
| Dallas IBC Production | Event Bus | Broadcast events, match milestones | Kafka |

### 9.2 Venue System Integrations

```mermaid
graph LR
    subgraph PROMPTWAR["PromptWar Integration Hub"]
        ADAPTER["Venue Adapter Layer\nPer-venue API normalization"]
    end

    subgraph VENUE_SYSTEMS["Venue Systems - per stadium"]
        TKT["Ticketing System\nScanned tickets, seat data"]
        ACC["Access Control\nGate entry, exit counts"]
        CCTV2["CCTV System\nRTSP streams"]
        POS["Point of Sale\nConcession transactions"]
        MAINT["Maintenance CMMS\nWork orders, equipment"]
        PARKING["Parking System\nAvailability, occupancy"]
    end

    TKT --> ADAPTER
    ACC --> ADAPTER
    CCTV2 --> ADAPTER
    POS --> ADAPTER
    MAINT --> ADAPTER
    PARKING --> ADAPTER
```

### 9.3 External Service Integrations

| Service | Provider | Use Case | SLA |
|---------|----------|---------|-----|
| LLM (Primary) | OpenAI GPT-4o | Fan chat, ops copilot | 99.9% uptime |
| LLM (Secondary) | Google Gemini 1.5 Pro | Fallback, multilingual | 99.9% uptime |
| Translation | DeepL / Google Translate API | Document translation | 99.9% uptime |
| Maps | Mapbox | Indoor + outdoor navigation | 99.9% uptime |
| SMS Fallback | Twilio | Offline fan notifications | 99.9% uptime |
| Public Transit | Local transit APIs (MTA, TTC, STE) | Multimodal journey | Best-effort |
| Ride-share | Uber / Lyft API | Post-match transport | 99% uptime |
| Weather | NOAA, Environment Canada, SMN | Forecast-aware planning | 99% uptime |
| Push Notifications | Firebase (Android) / APNs (iOS) | Fan app notifications | 99.9% uptime |

---

## 10. DevOps & MLOps

### 10.1 CI/CD Pipeline

```mermaid
flowchart LR
    subgraph DEV["Development"]
        CODE["Code Push\ngit push"] --> PR["Pull Request\nCode Review"]
        PR --> LINT["Linting +\nUnit Tests"]
    end

    subgraph CI["Continuous Integration"]
        LINT --> BUILD["Docker Build\nMulti-arch"]
        BUILD --> SCAN["Security Scan\nTrivy + Snyk"]
        SCAN --> INT_TEST["Integration Tests\nTestcontainers"]
        INT_TEST --> LOAD_TEST["Load Test\nk6 at 10K RPM"]
    end

    subgraph CD["Continuous Deployment"]
        LOAD_TEST -->|"Pass"| STAGING["Deploy to Staging\nKubernetes"]
        STAGING --> SMOKE["Smoke Tests\nAI response validation"]
        SMOKE -->|"Pass"| PROD["Deploy to Production\nBlue-Green rollout"]
        PROD --> MONITOR["Monitoring\nDatadog / Grafana"]
    end
```

### 10.2 MLOps Pipeline

```mermaid
flowchart TD
    DATA["Training Data\nVersioned in DVC"] --> TRAIN["Model Training\nSageMaker / Vertex AI"]
    TRAIN --> EVAL2["Evaluation\n- Accuracy metrics\n- Bias scores\n- Safety checks\n- Latency benchmarks"]
    EVAL2 -->|"Pass all gates"| REGISTRY["Model Registry\nMLflow\nSigned + versioned"]
    EVAL2 -->|"Fail"| BACK["Back to\ndata improvement"]
    REGISTRY --> SHADOW["Shadow Deployment\n5% traffic\n1 week"]
    SHADOW --> CANARY["Canary Release\n25% traffic\n48 hours"]
    CANARY -->|"KPIs stable"| FULL["Full Rollout\n100% traffic"]
    FULL --> MONITOR2["Production Monitoring\n- Hallucination rate\n- Latency drift\n- Accuracy drift\n- Bias monitoring"]
    MONITOR2 -->|"Drift detected"| DATA
```

### 10.3 Observability Stack

| Layer | Tool | Metrics |
|-------|------|---------|
| Infrastructure | Datadog / CloudWatch | CPU, memory, network, disk |
| Application | Datadog APM | Request rate, error rate, latency |
| LLM/AI | Custom + Langfuse | Hallucination rate, token usage, response quality |
| Business | Grafana | Fan satisfaction, adoption, NPS |
| Security | Falco + SIEM | Anomaly detection, access logs |
| Alerting | PagerDuty | 24/7 NOC escalation |

---

## 11. Compliance & Regulatory Requirements

### 11.1 Data Privacy Requirements by Region

```mermaid
graph TD
    subgraph US["United States"]
        US1["CCPA / CPRA\nCalifornia residents"]
        US2["ADA\nDigital accessibility"]
        US3["Illinois BIPA\nBiometric data ban\nin IL without consent"]
        US4["State privacy laws\nCT, CO, VA, TX, etc."]
    end

    subgraph CA["Canada"]
        CA1["PIPEDA\nFederal privacy law"]
        CA2["Quebec Law 25\nStricter than PIPEDA"]
        CA3["BC PIPA\nBC provincial"]
        CA4["AODA\nOntario accessibility"]
    end

    subgraph MX["Mexico"]
        MX1["LFPDPPP\nFederal data protection"]
        MX2["NOM standards\nAccessibility"]
    end

    subgraph GLOBAL["Global / Best Practice"]
        GL1["EU AI Act\nHigh-risk AI classification"]
        GL2["WCAG 2.1 AA\nAll digital interfaces"]
        GL3["ISO 21542\nBuilt environment access"]
    end

    US1 & US2 & US3 & US4 --> IMPL["Technical Implementation\n- Data residency by design\n- No biometrics without consent\n- Right to deletion API\n- Consent management platform\n- WCAG 2.1 AA on all UIs\n- Accessibility testing with\n  disability community"]
    CA1 & CA2 & CA3 & CA4 --> IMPL
    MX1 & MX2 --> IMPL
    GL1 & GL2 & GL3 --> IMPL
```

### 11.2 AI-Specific Compliance

| Requirement | Implementation |
|------------|---------------|
| AI Transparency | Clear "AI-powered" disclosure in all chat UIs |
| Explainability | All ops recommendations include "why" (e.g., "crowd density 85% because camera detects 4200 people/min") |
| Human-in-loop | Security and safety recommendations require human approval before action |
| Bias Testing | All language models tested on 50+ language pairs; CV models tested across skin tones, body types |
| Audit Logging | All AI decisions logged with model version, inputs, outputs, and confidence scores |
| Model Documentation | Model cards for all production AI models (following Google Model Card standards) |

---

## 12. Technology Stack Summary

```mermaid
graph LR
    subgraph FRONTEND["Frontend"]
        F1["React Native\nFan Mobile App"]
        F2["Next.js\nOps Dashboard"]
        F3["ARCore / ARKit\nAR Wayfinding"]
    end

    subgraph BACKEND["Backend Services"]
        B1["Python FastAPI\nMicroservices"]
        B2["LangChain / LlamaIndex\nLLM Orchestration"]
        B3["vLLM / TGI\nLLM Serving"]
        B4["Kong\nAPI Gateway"]
    end

    subgraph AI_ML["AI / ML"]
        AI1["GPT-4o / Gemini 1.5\nCloud LLM"]
        AI2["Llama 3 70B / 8B\nSelf-hosted LLM"]
        AI3["YOLOv8x\nComputer Vision"]
        AI4["Whisper v3\nSpeech Recognition"]
        AI5["Pinecone\nVector DB for RAG"]
    end

    subgraph DATA_LAYER["Data"]
        D1["Apache Kafka\nEvent Streaming"]
        D2["Apache Flink\nStream Processing"]
        D3["PostgreSQL\nOperational DB"]
        D4["Redis\nCache"]
        D5["S3 / Azure Blob\nData Lake"]
        D6["BigQuery\nAnalytics"]
    end

    subgraph INFRA["Infrastructure"]
        I1["Kubernetes\nContainer Orchestration"]
        I2["Istio\nService Mesh / mTLS"]
        I3["Terraform\nInfrastructure as Code"]
        I4["NVIDIA Jetson\nEdge AI Hardware"]
        I5["AWS + Azure + GCP\nMulti-cloud"]
    end

    subgraph MLOPS["MLOps"]
        M1["MLflow\nModel Registry"]
        M2["DVC\nData Versioning"]
        M3["Langfuse\nLLM Observability"]
        M4["Datadog\nAPM + Monitoring"]
    end
```

### Full Stack Reference Table

| Layer | Technology | Justification |
|-------|-----------|---------------|
| Fan Mobile | React Native | Cross-platform iOS/Android; AR support |
| Operations UI | Next.js + TypeScript | SSR, real-time dashboards |
| AR | ARCore (Android) + ARKit (iOS) | Native AR performance |
| API Gateway | Kong | Rate limiting, auth, routing at scale |
| Backend Services | Python FastAPI | Async, high-performance, ML-native |
| LLM Orchestration | LangChain + LlamaIndex | RAG, agent workflows, tool calling |
| LLM Serving | vLLM / TGI | Continuous batching, high throughput |
| Cloud LLM | GPT-4o + Gemini 1.5 Pro | Best multilingual, multimodal capability |
| Edge LLM | Llama 3 8B (4-bit quantized) | Edge-deployable, data sovereign |
| Computer Vision | YOLOv8x + OpenCV | Proven crowd detection performance |
| Speech | Whisper v3 + Azure TTS | Best STT accuracy, 100+ TTS voices |
| Vector DB | Pinecone | Managed, low-latency, scale-tested |
| Event Streaming | Apache Kafka | Industry standard, 1M+ events/sec |
| Stream Processing | Apache Flink | Sub-second latency, stateful joins |
| Operational DB | PostgreSQL + TimescaleDB | ACID, time-series extension |
| Cache | Redis Cluster | < 1ms latency for hot data |
| Data Lake | S3 / Azure Blob / GCS | Multi-cloud data residency |
| Analytics | BigQuery / Snowflake | Column-store analytics at petabyte scale |
| Containers | Kubernetes + Helm | Portable, autoscaling deployments |
| Service Mesh | Istio | mTLS, traffic management, observability |
| IaC | Terraform | Multi-cloud infrastructure management |
| Edge Hardware | NVIDIA Jetson Orin | GPU-accelerated CV at stadium edge |
| CI/CD | GitHub Actions + ArgoCD | GitOps, automated deployments |
| Model Registry | MLflow | Model versioning, A/B testing |
| Observability | Datadog + Grafana + Langfuse | Full-stack + LLM-specific monitoring |
| Security | Falco + Snyk + AWS Shield | Runtime security + dependency scanning |

---

*Document prepared by the PromptWar Architecture Team.*  
*Next Review: Architecture Gate G1 — Month 2*
