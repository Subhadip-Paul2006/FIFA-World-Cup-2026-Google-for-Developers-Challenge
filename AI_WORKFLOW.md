# 🤖 AI Workflow Document
## PromptWar — GenAI Smart Stadium Orchestration Platform
### FIFA World Cup 2026 · AI Systems Design & Agent Architecture

**Document Version:** 1.0  
**Date:** July 2026  
**Status:** Draft  
**Owner:** AI/ML Team  
**Classification:** Internal

---

## Table of Contents

1. [AI System Overview](#1-ai-system-overview)
2. [Agent Architecture](#2-agent-architecture)
3. [LLM Orchestration & RAG Pipeline](#3-llm-orchestration--rag-pipeline)
4. [Computer Vision Workflows](#4-computer-vision-workflows)
5. [Multilingual AI Pipeline](#5-multilingual-ai-pipeline)
6. [Predictive Analytics Workflows](#6-predictive-analytics-workflows)
7. [Model Training & Evaluation](#7-model-training--evaluation)
8. [AI Safety & Guardrails](#8-ai-safety--guardrails)
9. [AI Monitoring & Feedback Loops](#9-ai-monitoring--feedback-loops)
10. [Model Catalog](#10-model-catalog)

---

## 1. AI System Overview

### 1.1 AI Capability Map

```mermaid
mindmap
  root((PromptWar AI))
    Generative AI
      LLM Copilot
        Operations NL Queries
        Incident Report Generation
        Executive Briefings
      Fan Chat Assistant
        Navigation Guidance
        FAQ and Info
        Accessibility Support
      Dynamic Content
        Multilingual Signage
        AR Labels and Overlays
        Personalized Notifications
    Perception AI
      Computer Vision
        Crowd Density Detection
        Zone Occupancy Counting
        Object and Incident Detection
      Speech Processing
        STT Voice Commands
        TTS Announcements
        Accent and Dialect Handling
    Predictive AI
      Crowd Forecasting
        Arrival Wave Prediction
        Bottleneck Early Warning
        Post-match Egress Modeling
      Resource Optimization
        Staff Deployment
        Concession Demand
        Energy Load Prediction
      Transport Intelligence
        Shuttle Route Optimization
        Parking Availability
        Public Transit Demand
    Decision Support
      Scenario Simulation
        What-if Gate Closure
        Weather Impact Modeling
        Emergency Flow Planning
      Alert Intelligence
        Signal vs Noise Filtering
        Cross-venue Pattern Matching
        Escalation Routing
```

### 1.2 AI Processing Tiers

```mermaid
graph TB
    subgraph TIER1["Tier 1 — Edge AI (< 50ms)"]
        E1["Stadium Edge Server\nNVIDIA Jetson Orin"]
        E1a["YOLOv8 Crowd Detection"]
        E1b["Whisper STT - Voice Commands"]
        E1c["Llama 3 8B - Simple Queries"]
        E1d["AR Rendering Engine"]
        E1 --> E1a & E1b & E1c & E1d
    end

    subgraph TIER2["Tier 2 — Regional AI (< 200ms)"]
        R1["City/Metro Server Cluster"]
        R1a["Llama 3 70B - Complex NL Queries"]
        R1b["Crowd Flow Aggregation"]
        R1c["Cross-venue Event Correlation"]
        R1 --> R1a & R1b & R1c
    end

    subgraph TIER3["Tier 3 — Cloud AI (< 500ms)"]
        C1["Global Cloud - IBC Dallas"]
        C1a["GPT-4o / Gemini Pro - Premium Queries"]
        C1b["Global Analytics & Forecasting"]
        C1c["Model Training & Fine-tuning"]
        C1d["Executive Briefing Generation"]
        C1 --> C1a & C1b & C1c & C1d
    end

    TIER1 <-->|"Event stream\nAnomalies only"| TIER2
    TIER2 <-->|"Aggregated data\nComplex queries"| TIER3
```

---

## 2. Agent Architecture

### 2.1 Multi-Agent System Design

PromptWar uses a **multi-agent architecture** where specialized AI agents handle different domains, coordinated by an **Orchestrator Agent**.

```mermaid
graph TD
    USER_INPUT["User Input\nFan · Staff · Security · Volunteer"] --> ORCHESTRATOR

    subgraph ORCH["Orchestrator Agent"]
        ORCHESTRATOR["Master Orchestrator\nGPT-4o / Gemini Pro\nIntent Classification + Agent Routing"]
    end

    ORCHESTRATOR --> NAV_AGENT["Navigation Agent\nRAG + Mapbox\nWayfinding, accessibility routes"]
    ORCHESTRATOR --> CROWD_AGENT["Crowd Safety Agent\nCV + Predictive ML\nDensity alerts, forecasting"]
    ORCHESTRATOR --> TRANSLATE_AGENT["Translation Agent\nWhisper + LLM\nReal-time multilingual"]
    ORCHESTRATOR --> OPS_AGENT["Operations Copilot Agent\nRAG + Tool Use\nVenue data, incident mgmt"]
    ORCHESTRATOR --> TRANSPORT_AGENT["Transport Agent\nPredictive ML + APIs\nJourney planning, shuttles"]
    ORCHESTRATOR --> SUSTAIN_AGENT["Sustainability Agent\nAnalytical AI\nEnergy, waste, carbon"]
    ORCHESTRATOR --> EMERGENCY_AGENT["Emergency Response Agent\nPriority override\nEvacuation, safety"]

    NAV_AGENT --> RESP["Unified Response\nto User"]
    CROWD_AGENT --> RESP
    TRANSLATE_AGENT --> RESP
    OPS_AGENT --> RESP
    TRANSPORT_AGENT --> RESP
    SUSTAIN_AGENT --> RESP
    EMERGENCY_AGENT --> RESP
```

### 2.2 Orchestrator Agent Decision Flow

```mermaid
flowchart TD
    INPUT["Incoming Message\n'Where is the nearest\nwheelchair ramp?'"]

    CLASSIFY["Intent Classification\nTool: classify_intent()\nOutput: {domain, urgency, lang}"]

    INPUT --> CLASSIFY

    CLASSIFY --> DOMAIN{{"Domain?"}}
    DOMAIN -->|"NAVIGATION"| NAV["Route Navigation Agent\n+ Accessibility Router"]
    DOMAIN -->|"SAFETY / EMERGENCY"| EMERG["Emergency Agent\nPriority: CRITICAL\nHuman-in-loop required"]
    DOMAIN -->|"OPS_QUERY"| OPS["Ops Copilot Agent\nTool: query_venue_db()"]
    DOMAIN -->|"TRANSLATION"| TRANS["Translation Agent"]
    DOMAIN -->|"TRANSPORT"| TRANSP["Transport Agent"]
    DOMAIN -->|"GENERAL_INFO"| RAG_AGENT["RAG Search Agent\nvenue knowledge base"]

    NAV --> RESPONSE["Assemble Response\n+ Language + Format"]
    EMERG --> HUMAN["Human Operator\nApproval Required\nbefore any action"]
    OPS --> RESPONSE
    TRANS --> RESPONSE
    TRANSP --> RESPONSE
    RAG_AGENT --> RESPONSE

    RESPONSE --> OUTPUT["Delivered to User\nFan App · Dashboard · Voice"]
```

### 2.3 Operations Copilot Agent — Tool Use Design

The Ops Copilot uses **tool-calling (function calling)** to retrieve live data and take actions.

```mermaid
sequenceDiagram
    participant OPS_MGR as Ops Manager
    participant COPILOT as Ops Copilot Agent
    participant LLM as LLM (GPT-4o)
    participant TOOLS as Tools Layer
    participant DB as Venue Systems

    OPS_MGR->>COPILOT: "Show all incidents in Section 200\nin the last 30 minutes"
    COPILOT->>LLM: process(user_message, system_prompt, tool_definitions)
    LLM-->>COPILOT: tool_call: query_incidents(section="200", time_range="30min")
    COPILOT->>TOOLS: execute tool_call
    TOOLS->>DB: SELECT * FROM incidents WHERE zone='200' AND created_at > NOW()-30min
    DB-->>TOOLS: [{incident_id, type, severity, description, status}...]
    TOOLS-->>COPILOT: tool_result: [list of incidents]
    COPILOT->>LLM: continue(tool_result, format=natural_language)
    LLM-->>COPILOT: "There are 3 incidents in Section 200:\n1. Overcrowding at Row G - RESOLVED\n2. Medical assistance at Row M - IN PROGRESS\n3. Lost child report at Entry Gate - RESOLVED"
    COPILOT-->>OPS_MGR: Display formatted response + drill-down links
```

### 2.4 Available Tools for Ops Copilot Agent

| Tool Name | Description | Returns |
|-----------|-------------|---------|
| `query_crowd_density(zone_id, timestamp)` | Get real-time crowd density for a zone | Density %, count, status |
| `query_incidents(zone_id, time_range, severity)` | Retrieve incident logs with filters | List of incidents |
| `get_staff_locations(department, shift)` | Get current staff deployment map | Staff positions + status |
| `get_maintenance_alerts(venue_id, category)` | Retrieve open maintenance tickets | Ticket list + priority |
| `generate_incident_report(incident_id)` | Auto-draft an incident report | Draft report markdown |
| `send_staff_alert(zone_id, message, priority)` | Push alert to all staff in a zone | Confirmation |
| `get_concession_demand(zone_id)` | Predicted demand for next 60min | Demand forecast |
| `get_transport_status(venue_id)` | Real-time transport conditions outside venue | Transport summary |
| `simulate_scenario(event_type, zone_id)` | Run what-if crowd flow simulation | Simulation results |
| `get_energy_status(venue_id)` | Current energy consumption vs. targets | Energy dashboard data |

---

## 3. LLM Orchestration & RAG Pipeline

### 3.1 RAG Architecture (Per-Venue)

```mermaid
flowchart TD
    subgraph OFFLINE["Offline: Knowledge Base Preparation"]
        SOURCES["Source Documents\n- Venue floor plans PDF\n- Safety protocols\n- Match schedule\n- Transport guides\n- Accessibility routes\n- Staff manuals\n- Incident history"]
        CHUNK["Document Chunking\nRecursive character splitter\nChunk: 512 tokens\nOverlap: 64 tokens"]
        EMBED["Embedding Model\ntext-embedding-3-large\n3072 dimensions"]
        UPSERT["Vector Store Upsert\nPinecone - per-venue index\nWith metadata filters"]
        SOURCES --> CHUNK --> EMBED --> UPSERT
    end

    subgraph ONLINE["Online: Query Time"]
        QUERY["User Query\n'Where is Gate C restroom?'"]
        Q_EMBED["Query Embedding\nSame embedding model"]
        SEARCH["Similarity Search\nTop-5 chunks\nScore threshold > 0.72"]
        RERANK["Reranker\nCohereRerank or BGE\nImprove precision"]
        CONTEXT["Context Assembly\nSystem prompt + chunks\n+ user query + history"]
        GEN["LLM Generation\nGrounded response\nWith citations"]
        QUERY --> Q_EMBED --> SEARCH --> RERANK --> CONTEXT --> GEN
    end

    UPSERT -.->|"Index ready"| SEARCH
```

### 3.2 Prompt Engineering Strategy

#### System Prompt Template — Fan Assistant

```
You are PromptWar, the AI assistant for {venue_name} during FIFA World Cup 2026.

ROLE: Help fans with navigation, information, and accessibility in a friendly, concise manner.

LANGUAGE: Always respond in {user_language}. If unsure, use English.

CONTEXT:
- User's current zone: {current_zone}
- User's seat: {seat_info}
- Accessibility needs: {accessibility_profile}
- Current time: {timestamp}
- Match: {match_info}

KNOWLEDGE: Use only the provided context documents. If information is not in context, say you don't know and offer to connect the user with a staff member.

RULES:
1. Never invent locations, times, or factual information
2. Always include specific directions (left, right, distance in meters)
3. For accessibility requests, always prioritize routes without stairs or crowding
4. For emergencies, immediately provide the nearest exit and say "Please find the nearest staff member"
5. Keep responses under 150 words unless complex directions require more

SAFETY: If any message suggests an emergency or safety threat, immediately respond with emergency protocol and flag for human review.
```

#### System Prompt Template — Operations Copilot

```
You are the PromptWar Operations Copilot for {venue_name}, FIFA World Cup 2026.

ROLE: Support venue operations staff with real-time situational awareness, incident management, and decision support.

USER: {user_role} — {user_name}

CAPABILITIES: You have access to live data via tools. Always use tools to get current data; never guess or hallucinate numbers.

RULES:
1. Always retrieve live data before answering operational questions
2. For safety-critical recommendations, clearly state "HUMAN ACTION REQUIRED" and provide the reason
3. When generating reports, use the structured template format
4. Flag any anomaly that is 2 standard deviations above baseline as ALERT
5. Cross-reference incidents with nearby venues if relevant patterns detected

TONE: Professional, concise, action-oriented. Use bullet points for operational summaries.
```

### 3.3 LLM Routing Logic

```mermaid
flowchart TD
    QUERY["Incoming Query"] --> COMPLEXITY{"Query Complexity\nAnalysis"}

    COMPLEXITY -->|"Simple\nSingle-fact lookup\n< 50 tokens"| EDGE_LLM["Llama 3 8B\nEdge Node\nLatency: < 100ms\nCost: $0"]

    COMPLEXITY -->|"Moderate\nMulti-step reasoning\n50-200 tokens"| REGIONAL_LLM["Llama 3 70B\nRegional Server\nLatency: < 200ms\nCost: Low"]

    COMPLEXITY -->|"Complex\nMulti-tool, multilingual\nSafety-critical\n> 200 tokens"| CLOUD_LLM["GPT-4o / Gemini Pro\nCloud API\nLatency: < 500ms\nCost: Medium"]

    EDGE_LLM --> CONFIDENCE{"Confidence\nScore?"}
    CONFIDENCE -->|"< 0.7"| REGIONAL_LLM
    CONFIDENCE -->|">= 0.7"| RESP["Response to User"]

    REGIONAL_LLM --> CONFIDENCE2{"Confidence\nScore?"}
    CONFIDENCE2 -->|"< 0.75"| CLOUD_LLM
    CONFIDENCE2 -->|">= 0.75"| RESP

    CLOUD_LLM --> RESP
```

---

## 4. Computer Vision Workflows

### 4.1 Crowd Density Detection Pipeline

```mermaid
flowchart LR
    subgraph INPUT["Input"]
        CAM["45 CCTV Cameras\nper stadium\n1080p @ 30fps"]
    end

    subgraph PREPROCESS["Preprocessing - Edge GPU"]
        SAMPLE["Frame Sampling\n5 FPS extracted\n(150 frames/sec total)"]
        RESIZE["Resize to 640x640\nYOLO input format"]
        NORMALIZE["Normalize\nMean/Std ImageNet"]
    end

    subgraph DETECT["Detection - Edge GPU"]
        YOLO["YOLOv8x Inference\nClass: person\nConfidence > 0.5\nNMS IoU > 0.45"]
        COUNT["Person Count\nPer camera zone"]
        HEATMAP["Density Heatmap\nBilinear interpolation\nper stadium section"]
    end

    subgraph ANALYZE["Analysis"]
        COMPARE["Compare vs.\nSection Capacity\nLimits DB"]
        THRESHOLD{"Density\nThreshold?"}
        TREND["Trend Analysis\n5-min moving average\nIncreasing / Stable / Decreasing"]
    end

    subgraph OUTPUT["Output"]
        ALERT["Real-time Alert\n< 50ms total\nto WebSocket"]
        STORE2["Time-series Store\nInfluxDB\n30-day retention"]
        FORECAST["Feed LSTM\nForecasting Model\n15-min lookahead"]
    end

    CAM --> SAMPLE --> RESIZE --> NORMALIZE
    NORMALIZE --> YOLO --> COUNT --> HEATMAP
    HEATMAP --> COMPARE --> THRESHOLD
    THRESHOLD -->|"WARNING\n> 70% capacity"| ALERT
    THRESHOLD -->|"CRITICAL\n> 85% capacity"| ALERT
    THRESHOLD --> TREND --> STORE2 --> FORECAST
```

### 4.2 Incident Detection Pipeline

```mermaid
stateDiagram-v2
    [*] --> Monitoring: System Start
    Monitoring --> FrameCapture: Continuous 5fps

    FrameCapture --> PersonDetection: YOLOv8 Inference
    PersonDetection --> BehaviorAnalysis: Tracking + Velocity

    BehaviorAnalysis --> Normal: Velocity normal\nDensity < 70%
    Normal --> Monitoring: Continue

    BehaviorAnalysis --> Anomaly: Velocity spike\nOR density surge\nOR crowd reversal
    Anomaly --> AIClassification: VLM Scene Analysis\n"Describe what is happening"

    AIClassification --> FalseAlarm: Confidence < 60%\nOR benign classification
    FalseAlarm --> Monitoring: Log and continue

    AIClassification --> IncidentDetected: Confidence > 80%\nCritical classification
    IncidentDetected --> HumanReview: Alert ops dashboard\nHUMAN MUST APPROVE

    HumanReview --> Confirmed: Ops manager confirms
    HumanReview --> Dismissed: Ops manager dismisses
    Confirmed --> Dispatch: Auto-dispatch nearest staff\nGenerate incident ticket
    Dismissed --> Monitoring: Log false positive\nFeedback to model
    Dispatch --> Monitoring: Continue monitoring
```

### 4.3 Privacy-Preserving Computer Vision

```mermaid
flowchart TD
    RAW["Raw Camera Frame\n1080p with faces"] --> BLUR["Face Blur\nMediaPipe Face Detection\n+ OpenCV blur\nBEFORE any storage"]
    BLUR --> AGGREGATE["Aggregate Analysis Only\nNo individual tracking\nNo face recognition\nCount = person bounding boxes"]
    AGGREGATE --> OUTPUT2["Anonymized Outputs\n- Zone density score\n- Headcount\n- Movement direction\n- Velocity estimate"]
    OUTPUT2 --> STORE_ANON["Store anonymized data only\nNo raw frames stored\n> 30 seconds"]

    style BLUR fill:#ff6b6b,color:#fff
    style AGGREGATE fill:#51cf66,color:#fff
```

---

## 5. Multilingual AI Pipeline

### 5.1 End-to-End Translation Flow

```mermaid
flowchart LR
    subgraph INPUT2["Input - Any Language"]
        VOICE_IN["Voice Input\n'Dónde está\nel baño?'"]
        TEXT_IN["Text Input\n'كيف أصل\nإلى مقعدي؟'"]
    end

    subgraph STT_LAYER["Speech Processing"]
        WHISPER["Whisper v3 Large\nLanguage auto-detection\nWER < 5% for 50+ langs"]
    end

    subgraph PROC["Processing"]
        DETECT_LANG["Language Detection\nISO 639-1 code"]
        TRANSLATE_IN["Input Translation\n→ English (internal)\nDeepL API"]
        LLM_PROC["LLM Processing\nin English"]
        TRANSLATE_OUT["Response Translation\n→ User language\nDeepL API"]
    end

    subgraph OUTPUT2["Output - User Language"]
        TTS_OUT["TTS Output\nAzure Neural TTS\n100+ language voices"]
        TEXT_OUT["Text Response\nin user language"]
    end

    VOICE_IN --> WHISPER --> DETECT_LANG
    TEXT_IN --> DETECT_LANG
    DETECT_LANG --> TRANSLATE_IN --> LLM_PROC --> TRANSLATE_OUT
    TRANSLATE_OUT --> TTS_OUT
    TRANSLATE_OUT --> TEXT_OUT
```

### 5.2 Language Coverage Strategy

```mermaid
graph TD
    subgraph P0_LANGS["Priority 0 — Full Support\n28 languages"]
        L_EN["English"]
        L_ES["Spanish"]
        L_PT["Portuguese"]
        L_FR["French"]
        L_AR["Arabic"]
        L_ZH["Mandarin"]
        L_JA["Japanese"]
        L_KO["Korean"]
        L_DE["German"]
        L_MORE["...19 more"]
    end

    subgraph P1_LANGS["Priority 1 — Chat Only\n30+ languages"]
        L2["Hindi · Bengali · Russian\nDutch · Polish · Turkish\nSwahili · Vietnamese · Thai\n...and more"]
    end

    subgraph P2_LANGS["Priority 2 — Text Only\n50+ languages"]
        L3["Less common languages\nAuto-translation via\nGoogle Translate fallback"]
    end

    P0_LANGS --> FULL_FEAT["Full Feature Set\nVoice · Chat · AR\nSignage · TTS"]
    P1_LANGS --> CHAT_FEAT["Chat + Text\nNo real-time voice"]
    P2_LANGS --> TEXT_FEAT["Text translation only\nFallback to English\nfor safety messages"]
```

### 5.3 Sign Language Avatar Pipeline

```mermaid
flowchart TD
    TEXT_MSG["Text Message / Announcement\nin any language"] --> TRANSLATE_SL["Translate to\nStandard English\n(or target signed language)"]
    TRANSLATE_SL --> GLOSS["Gloss Generation\nConvert to sign language\ngloss notation\nASL / BSL / LSF / etc."]
    GLOSS --> AVATAR["3D Avatar Renderer\nCustom CGI avatar\nSign animation clips"]
    AVATAR --> BLEND["Motion Blending\nSmooth transitions\nbetween signs"]
    BLEND --> OUTPUT_SL["Output\n- Stadium screen overlay\n- Mobile AR\n- Kiosk display"]

    NOTE["Note: Sign language avatars\nreviewed by Deaf community\ntesters before deployment.\nNot a replacement for\nhuman interpreters for\ncomplex conversations."]
```

---

## 6. Predictive Analytics Workflows

### 6.1 Crowd Arrival Forecasting

```mermaid
flowchart TD
    subgraph FEATURES["Feature Engineering"]
        F_HIST["Historical Arrival Patterns\nSame venue, similar fixtures"]
        F_TICKET["Ticket Scan Rate\nReal-time gate entry counts"]
        F_TRANSPORT["Transport API Data\nBus loads, metro ridership"]
        F_WEATHER["Weather Forecast\nRain = earlier arrivals"]
        F_MATCH["Match Attributes\nKickoff time, rival teams"]
        F_SOCIAL["Social Sentiment\nHype level for match"]
    end

    subgraph MODEL["Forecasting Model"]
        LSTM_M["LSTM Network\n2-layer, hidden_size=256\nTrained on 10yr event data"]
        PROPHET["Facebook Prophet\nTrend + seasonality\nHoliday effects"]
        ENSEMBLE["Ensemble\nWeighted average\n60% LSTM + 40% Prophet"]
    end

    subgraph OUTPUT3["Forecast Output"]
        PRED15["15-min Prediction\nHigh confidence (σ < 5%)"]
        PRED30["30-min Prediction\nMedium confidence"]
        PRED60["60-min Prediction\nLow confidence (planning only)"]
    end

    FEATURES --> LSTM_M & PROPHET
    LSTM_M & PROPHET --> ENSEMBLE
    ENSEMBLE --> PRED15 & PRED30 & PRED60

    PRED15 --> ACTION["Operational Actions\n- Pre-position staff\n- Open additional gates\n- Activate extra concession lanes\n- Adjust shuttle frequency"]
```

### 6.2 Energy Optimization Workflow

```mermaid
flowchart TD
    subgraph SENSORS["Input Sensors"]
        OCCUPANCY["Occupancy Sensors\nPer zone real-time"]
        WEATHER_S["Weather Station\nTemp, humidity, cloud cover"]
        ENERGY_METER["Energy Meters\nPer circuit real-time kWh"]
        HVAC_STATE["HVAC State\nCurrent setpoints"]
    end

    subgraph AI_ENGINE["AI Optimization Engine"]
        FORECAST_OCC["Occupancy Forecast\n30-min ahead per zone"]
        ENERGY_MODEL["Energy Demand Model\nXGBoost regression"]
        RL_AGENT["RL Policy Agent\nOptimize: minimize energy\nConstraint: comfort + safety"]
    end

    subgraph RECOMMENDATIONS["Output Recommendations"]
        HVAC_REC["HVAC Setpoints\nZone-by-zone schedule"]
        LIGHT_REC["Lighting Schedule\nDynamic dimming"]
        LOAD_REC["Load Shifting\nNon-critical systems\noff-peak scheduling"]
    end

    SENSORS --> AI_ENGINE
    AI_ENGINE --> RECOMMENDATIONS
    RECOMMENDATIONS --> HUMAN_APPROVE["Facilities Manager\nApproves changes\n(auto-approve within ±2°C)"]
    HUMAN_APPROVE --> ACTUATE["BMS Integration\nBuilding Management System\nActuates changes"]
    ACTUATE --> FEEDBACK["Feedback Loop\nActual vs. predicted\nModel refinement"]
    FEEDBACK --> AI_ENGINE
```

### 6.3 Transport Demand Prediction & Shuttle Optimization

```mermaid
flowchart LR
    subgraph INPUTS["Transport Inputs"]
        TICKET_DATA["Ticket Data\nFan location clusters\nby transport mode"]
        HISTORICAL["Historical Transport\nPast match patterns"]
        REAL_TIME_T["Real-time Demand\nApp ride requests\nMobile check-ins"]
    end

    subgraph PRED["Demand Prediction"]
        CLUSTER["Fan Cluster Analysis\nWhere fans are going\npost-match"]
        DEMAND_MODEL["Gradient Boosting\nDemand per route\n15-min intervals"]
    end

    subgraph OPT["Route Optimization"]
        RL_TRANSPORT["RL Agent\nShuttle fleet routing\nObjective: minimize wait time\nConstraint: fleet size"]
    end

    subgraph ACTION2["Actions"]
        SHUTTLE_SCHED["Dynamic Shuttle\nSchedule Updates"]
        DRIVER_APP["Driver App\nUpdated routes\npush to drivers"]
        FAN_NOTIFY["Fan Notifications\n'Your shuttle departs Gate D\nin 8 minutes'"]
    end

    INPUTS --> PRED --> OPT --> ACTION2
```

---

## 7. Model Training & Evaluation

### 7.1 Training Data Strategy

```mermaid
graph TD
    subgraph SOURCES2["Training Data Sources"]
        S1["Historical World Cup data\nRussia 2018, Qatar 2022"]
        S2["Past 2026 venue events\n(NFL, concerts, etc.)"]
        S3["Synthetic data\nSimulated crowd scenarios"]
        S4["Publicly available\nstadium datasets"]
        S5["Partner venue data\n(anonymized, consented)"]
        S6["Multilingual fan queries\n(simulated + real FAQs)"]
    end

    subgraph PROCESS["Data Processing Pipeline"]
        COLLECT["Data Collection\n& Ingestion"] --> CLEAN2["Data Cleaning\nDe-dup, outliers, PII removal"]
        CLEAN2 --> ANNOTATE["Annotation\nHuman + AI-assisted labeling"]
        ANNOTATE --> VALIDATE["Quality Validation\nInter-annotator agreement > 0.85"]
        VALIDATE --> VERSION["DVC Versioning\nReproducible datasets"]
    end

    SOURCES2 --> PROCESS
```

### 7.2 Evaluation Framework

```mermaid
flowchart TD
    subgraph METRICS_LLM["LLM Metrics"]
        M1["Accuracy\nFactual correctness vs. ground truth"]
        M2["Hallucination Rate\n% responses with invented facts"]
        M3["Latency P95\n< 500ms target"]
        M4["User Satisfaction\nLikert 1-5 from beta users"]
        M5["Multilingual Quality\nBLEU score per language pair"]
    end

    subgraph METRICS_CV["Computer Vision Metrics"]
        CV_M1["mAP\nMean Average Precision\n> 0.85 target"]
        CV_M2["Count Error\nMAE vs. ground truth counts\n< 5% target"]
        CV_M3["Inference Latency\n< 50ms per frame target"]
        CV_M4["Bias Testing\nAccuracy across skin tones\n< 5% variance"]
    end

    subgraph METRICS_PRED["Predictive Model Metrics"]
        PR_M1["MAPE\nMean Absolute % Error\n< 10% for crowd forecasts"]
        PR_M2["Coverage\n% of surges caught\n15min in advance > 80%"]
        PR_M3["False Alarm Rate\n< 5% false CRITICAL alerts"]
    end

    subgraph SAFETY_CHECKS["Safety & Bias Checks"]
        BIAS1["Language Bias Test\nPerformance parity across 50 langs"]
        BIAS2["Demographic Bias Test\nCV accuracy across demographics"]
        SAFETY1["Jailbreak Resistance\nPrompt injection testing"]
        SAFETY2["Harmful Content\nToxicity filter validation"]
    end

    METRICS_LLM & METRICS_CV & METRICS_PRED & SAFETY_CHECKS --> GATE["Model Release Gate\nAll metrics must pass\nbefore production"]
```

### 7.3 Continuous Learning Strategy

```mermaid
flowchart TD
    PROD["Production Model\nin Live Deployment"]

    subgraph FEEDBACK["Feedback Collection"]
        USER_FB["User Ratings\nThumb up/down on AI responses"]
        OPS_FB["Ops Feedback\nStaff override logging"]
        AUTO_FB["Automated Signals\nTask completion rate\nQuery re-ask rate"]
    end

    subgraph ANALYSIS["Feedback Analysis"]
        PATTERN["Pattern Analysis\nWhat types of queries fail?"]
        DRIFT["Distribution Drift Detection\nKS test on query distributions"]
        PERF["Performance Monitoring\nLatency, accuracy, bias drift"]
    end

    subgraph RETRAIN["Retraining Trigger"]
        AUTO_RETRAIN["Automated Retrain\nif drift > threshold\nor weekly scheduled"]
        HUMAN_REVIEW["Human Review\nNew edge cases added\nto training set"]
    end

    PROD --> FEEDBACK
    FEEDBACK --> ANALYSIS
    ANALYSIS --> RETRAIN
    RETRAIN --> NEW_MODEL["New Model Candidate\nEvaluation pipeline"]
    NEW_MODEL -->|"Pass evaluation"| DEPLOY_NEW["Deploy via Canary\nShadow → 25% → 100%"]
    DEPLOY_NEW --> PROD
```

---

## 8. AI Safety & Guardrails

### 8.1 Safety Architecture

```mermaid
graph TD
    USER_INPUT2["User Input"] --> INPUT_GUARD["Input Guardrails\n1. PII Detection & Masking\n2. Toxicity Filter\n3. Prompt Injection Detection\n4. Length Limits"]

    INPUT_GUARD --> LLM_PROC2["LLM Processing\nWith system prompt\nconstraints"]

    LLM_PROC2 --> OUTPUT_GUARD["Output Guardrails\n1. Hallucination Detector\n2. Factual Consistency Check\n3. Toxicity Filter\n4. PII Leakage Check\n5. Confidence Threshold Check"]

    OUTPUT_GUARD -->|"Confidence > 0.75\nAll checks pass"| USER_RESP["Response\nto User"]

    OUTPUT_GUARD -->|"Confidence < 0.75\nOR check fails"| FALLBACK["Fallback Response\n'I'm not sure. Let me\nconnect you with staff.'"]

    OUTPUT_GUARD -->|"Safety-critical\nOR Emergency"| HUMAN_ESCALATE["Escalate to\nHuman Operator\nIMMEDIATELY"]
```

### 8.2 Human-in-the-Loop Protocol

```mermaid
flowchart TD
    AI_REC["AI Recommendation\nor Alert Generated"]

    SEVERITY{"Severity Level?"}
    AI_REC --> SEVERITY

    SEVERITY -->|"INFO / LOW"| AUTO_DISPLAY["Auto-display on dashboard\nOps can acknowledge\nor dismiss"]

    SEVERITY -->|"MEDIUM"| SOFT_APPROVAL["Soft Approval Required\nOps dashboard notification\n90-second auto-approve\nif no response"]

    SEVERITY -->|"HIGH"| HARD_APPROVAL["Hard Approval Required\nExplicit ops confirmation\nNO auto-approve\nAI explains reasoning"]

    SEVERITY -->|"CRITICAL / EMERGENCY"| MANDATORY_HUMAN["MANDATORY Human Action\nSiren + alert to all supervisors\nAI CANNOT auto-execute\nFull audit log captured"]

    AUTO_DISPLAY --> LOG["Audit Log\nAll AI recommendations\nand human decisions"]
    SOFT_APPROVAL --> LOG
    HARD_APPROVAL --> LOG
    MANDATORY_HUMAN --> LOG
```

### 8.3 Guardrail Rules

| Rule | Type | Trigger | Action |
|------|------|---------|--------|
| PII Masking | Input | Detected name/email/phone in query | Mask before LLM processing |
| Prompt Injection | Input | Known injection patterns detected | Block + log + alert security |
| Hallucination | Output | Response contradicts retrieved facts | Downgrade to fallback response |
| Confidence Floor | Output | Model confidence < 0.70 | Add uncertainty disclaimer |
| Toxicity Filter | Input/Output | Toxic content score > 0.6 | Block + log + human review |
| Emergency Override | Output | Emergency keywords detected | Hard-route to human + emergency protocol |
| Language Safety | Output | Content inappropriate in target culture | Block + cultural review flag |
| Data Residency | Runtime | Cross-border data access detected | Block + alert privacy officer |

---

## 9. AI Monitoring & Feedback Loops

### 9.1 Real-time AI Dashboard

```mermaid
graph LR
    subgraph METRICS["Live AI Metrics Dashboard"]
        M_LAT["Latency P50/P95/P99\nper service"]
        M_ERR["Error Rates\nby error type"]
        M_HALL["Hallucination Rate\nrolling 1hr window"]
        M_SAT["User Satisfaction\nrolling NPS"]
        M_CROWD["Crowd Alert Accuracy\n% confirmed vs. false alarms"]
        M_LANG["Language Distribution\nof active queries"]
        M_COST["LLM Token Usage\n& cost per minute"]
    end

    subgraph ALERTS2["Alerting Rules"]
        A1["Latency P95 > 800ms\n→ PagerDuty WARN"]
        A2["Error rate > 5%\n→ PagerDuty CRITICAL"]
        A3["Hallucination rate > 2%\n→ Slack + investigation"]
        A4["False alarm CV > 10%\n→ CV team alert"]
    end

    METRICS --> ALERTS2
```

### 9.2 Offline Evaluation Schedule

| Evaluation | Frequency | Metrics | Owner |
|------------|-----------|---------|-------|
| LLM Accuracy Benchmark | Weekly | Accuracy, hallucination rate, BLEU | AI/ML Lead |
| CV Performance Test | Weekly | mAP, count error, latency | CV Engineer |
| Bias Audit | Monthly | Cross-language parity, demographic CV parity | AI Ethics Lead |
| Red Team / Adversarial | Monthly | Jailbreak resistance, injection robustness | Security |
| User Satisfaction Survey | Post-match | NPS, qualitative feedback | Product |
| Full System Load Test | Pre-gate | End-to-end P95 latency at 100K users | SRE |

---

## 10. Model Catalog

### 10.1 Production Models

```mermaid
graph LR
    subgraph CATALOG["PromptWar Model Catalog v1.0"]
        subgraph LLM_CAT["LLM Models"]
            M_GPT["gpt-4o-2024-11-20\nCloud LLM Primary\nFan chat + ops copilot"]
            M_GEM["gemini-1.5-pro-002\nCloud LLM Secondary\nMultilingual fallback"]
            M_LLAMA70["llama-3-70b-instruct-promptwar-v1\nFine-tuned Regional LLM\nOps-specific vocabulary"]
            M_LLAMA8["llama-3-8b-instruct-q4\nEdge LLM\n4-bit quantized for Jetson"]
        end
        subgraph CV_CAT["Computer Vision Models"]
            M_YOLO["yolov8x-crowd-v2\nCustom fine-tuned\nStadium crowd domain"]
            M_DENSITY["crowd-density-cnn-v1\nDensity heatmap model\nTrained on synthetic + real data"]
        end
        subgraph SPEECH_CAT["Speech Models"]
            M_WHISPER["whisper-large-v3\nMultilingual STT\n99 language support"]
            M_TTS["azure-neural-tts\n100+ voices\n47 languages real-time"]
        end
        subgraph PRED_CAT["Predictive Models"]
            M_CROWD_PRED["crowd-arrival-lstm-v3\nFine-tuned on FIFA 2018/2022\n+ 2026 venue pre-season data"]
            M_ENERGY["energy-optimizer-xgb-v1\nVenue energy demand\nper zone forecast"]
            M_TRANSPORT["transport-demand-gb-v1\nPost-match transport\ndemand per route"]
        end
    end
```

### 10.2 Model Card Summary

| Model | Task | Accuracy | Latency | Bias Status | Last Updated |
|-------|------|---------|---------|-------------|-------------|
| gpt-4o-2024-11-20 | General LLM | 94% task completion | 380ms P95 | External audit pending | Rolling |
| llama-3-70b-instruct-promptwar-v1 | Ops queries | 91% task completion | 185ms P95 | Bias test passed (50 langs) | Month 3 |
| llama-3-8b-instruct-q4 | Edge simple queries | 84% task completion | 95ms P95 | Bias test passed | Month 3 |
| yolov8x-crowd-v2 | Crowd detection | mAP 0.88 | 42ms P99 | Skin tone parity < 3% delta | Month 4 |
| whisper-large-v3 | STT | WER 4.2% avg | 180ms | Language parity tested | OpenAI rolling |
| crowd-arrival-lstm-v3 | Crowd forecasting | MAPE 7.8% | 250ms batch | N/A (aggregate) | Month 4 |

---

## Appendix A: AI Incident Response Playbook

```mermaid
flowchart TD
    INCIDENT["AI System Incident\nDetected"] --> CLASSIFY2{"Incident\nClassification"}

    CLASSIFY2 -->|"Model Hallucination"| HALL_RESP["1. Disable affected model endpoint\n2. Route to fallback LLM\n3. Notify AI team\n4. Collect failed examples\n5. Patch + redeploy"]

    CLASSIFY2 -->|"CV False Alarm Surge"| CV_RESP["1. Reduce CV alert sensitivity\n2. Require human confirmation on ALL alerts\n3. Ops team manual monitoring\n4. Investigate camera feed quality"]

    CLASSIFY2 -->|"Latency Spike"| LAT_RESP["1. Check autoscaler\n2. Reduce to regional LLM\n3. Enable cached-response mode\n4. Throttle non-critical features"]

    CLASSIFY2 -->|"Security Breach"| SEC_RESP["1. IMMEDIATE: Revoke all API keys\n2. Enable break-glass auth\n3. Notify security team\n4. Engage FIFA security liaison\n5. Forensic investigation"]

    CLASSIFY2 -->|"Bias Issue Discovered"| BIAS_RESP["1. Disable affected feature\n2. Notify diversity/ethics team\n3. Pull affected model from production\n4. Root cause analysis\n5. Re-test and community review before re-enable"]
```

---

## Appendix B: AI Cost Model (Estimated)

| Component | Estimated Monthly Cost | Notes |
|-----------|----------------------|-------|
| GPT-4o API calls | $45,000 - $80,000 | Fan chat + ops, varies with load |
| Gemini Pro API | $10,000 - $20,000 | Fallback + multilingual |
| Azure TTS | $8,000 - $15,000 | 100+ language voice synthesis |
| Whisper (self-hosted) | $3,000 server cost | Edge deployment |
| Llama 3 70B (self-hosted) | $8,000 GPU server | Regional cluster |
| Pinecone Vector DB | $2,000 - $5,000 | Per-venue indices |
| Edge hardware (amortized) | $15,000 | 16x Jetson Orin + GPU |
| **Total Estimated** | **$91,000 - $143,000/month** | Peak during tournament months |

> **Cost Optimization:** Edge LLM routing handles ~60% of queries at near-zero incremental cost, reducing cloud LLM calls significantly.

---

*Document prepared by the PromptWar AI/ML Team.*  
*Next Review: Post-MVP Pilot (Gate G2 — Month 5)*
