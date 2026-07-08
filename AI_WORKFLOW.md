# 🤖 AI Workflow Document
## PromptWar — GenAI Smart Stadium Orchestration Platform
### FIFA World Cup 2026 · AI Systems Design

**Document Version:** 1.0  
**Status:** Hackathon MVP  

---

## 1. AI System Overview

The PromptWar MVP relies on a streamlined AI workflow powered by the **Gemini API**. We use Generative AI to handle fan inquiries, process staff voice reports, and summarize operational data.

Instead of complex distributed AI clusters, the MVP centralizes AI logic within a robust backend (FastAPI), utilizing Retrieval-Augmented Generation (RAG) to ground the AI in our simulated venue data.

---

## 2. Core AI Workflows

### 2.1 Fan Assistant Workflow
When a fan needs help (e.g., finding a seat or translating an announcement), the workflow is:

1. **Fan Asks Question**: "Where is the nearest wheelchair-accessible restroom?" (Can be in any language).
2. **Backend Processing**: Request hits the FastAPI server.
3. **RAG Retrieval**: The server queries the Vector DB (ChromaDB/Pinecone) for the specific stadium layout and accessibility nodes.
4. **Gemini API**: The prompt, user context, and retrieved map data are sent to Gemini.
5. **Response**: Gemini generates a localized, accurate set of directions and returns it to the fan's device.

### 2.2 Ops Copilot Workflow
When an operations manager needs situational awareness:

1. **Ops Asks Question**: "Summarize the incidents from the last 30 minutes and recommend actions."
2. **Knowledge Retrieval**: FastAPI pulls the latest simulated incident logs from the PostgreSQL database.
3. **Gemini API**: The logs are passed to Gemini with a system prompt instructing it to act as an operations expert.
4. **Recommendation**: Gemini returns a structured summary and specific recommendations (e.g., "Dispatch medical to Gate C").

### 2.3 Voice Incident Reporting Workflow
To help volunteers and security report issues hands-free:

1. **Voice Input**: Staff member speaks into the app: "We have a large spill and slippery floor at Section 104."
2. **Speech-to-Text**: The audio is transcribed into text using standard STT APIs (or Web Speech API).
3. **Gemini API**: The transcript is sent to Gemini to extract metadata (Location: Section 104, Type: Maintenance, Severity: Low).
4. **Incident Report**: The structured data is saved to the database and immediately appears on the Crowd Analytics Dashboard.

---

## 3. Simulation & Data Grounding

Because we are building an MVP, we do not use live computer vision or massive IoT streams.

- **Crowd Simulation**: We use a background script to generate dynamic JSON data representing crowd density. Gemini can read this JSON to answer questions like "Is Gate A crowded?"
- **No Hallucinations**: By strictly enforcing RAG and providing Gemini with rigid system prompts, we ensure the AI only answers based on the provided simulated dataset and demo venue map.
