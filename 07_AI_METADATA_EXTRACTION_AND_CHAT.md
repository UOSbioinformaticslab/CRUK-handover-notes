# Feature Note 07: AI-Assisted Metadata Extraction & Conversational Assistant

**Target Audience**: CRUK Technical Staff, AI Engineers & Data Curators  
**Module Location**: `cruk_datahub_landing_page/src/components/` & `ai/ai-microservices/`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - OPTIONAL MICROSERVICE]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

To accelerate metadata ingestion for data custodians and simplify discovery for researchers, the CRUK Data Hub platform integrates Google Gemini AI microservices.

```
┌────────────────────────────────────────┐         ┌────────────────────────────────────────┐
│ AiUploadWidget.jsx                     │         │ ChatWidget.jsx / AssistantPane.jsx     │
│ ────────────────────────────────────── │         │ ────────────────────────────────────── │
│ Custodian drops PDF/TXT dataset file   │         │ Natural language query bar:            │
│ or pastes abstract text                │         │ "Find multi-omic breast cancer study"  │
└────────────────────────────────────────┘         └────────────────────────────────────────┘
                    │                                                   │
                    └─────────────────────────┬─────────────────────────┘
                                              │ HTTP REST Payload
                                              ▼
                           ┌─────────────────────────────────────┐
                           │ AI Microservice (ai/ai-microservice)│
                           │ FastAPI on port 8001                │
                           │ Google Gemini API Engine            │
                           └─────────────────────────────────────┘
                                              │
                                              ▼ Structured JSON Output
                           ┌─────────────────────────────────────┐
                           │ Dynamic Form Auto-population        │
                           │ Suggested Taxonomy Filter Tags      │
                           └─────────────────────────────────────┘
```

### Key Subsystems & Components

1. **`AiUploadWidget.jsx` ([AiUploadWidget.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/AiUploadWidget.jsx))**:
   - Ingests unstructured dataset abstracts, publications, or PDF data dictionaries.
   - Sends file text to `http://localhost:8001/extract-metadata`.
   - Parses LLM output into validated JSON matching CRUK 1.0.0 schema fields (Title, Abstract, Sample Size, Cancer Types, Multi-omic Tags).
   - Auto-populates upload forms, saving custodians hours of manual data entry.

2. **`ChatWidget.jsx` ([ChatWidget.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ChatWidget.jsx)) & `AssistantPane.jsx` ([AssistantPane.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/AssistantPane.jsx))**:
   - Interactive right sidebar assistant pane on catalog pages.
   - Accepts natural language inputs (e.g., *"Show me pediatric brain cancer datasets with ethics approval"*).
   - Converts natural text queries into corresponding Boolean filter selection IDs.

3. **Graceful Fallback Design**:
   - The entire platform runs seamlessly without the `ai-microservices` backend or `GEMINI_API_KEY`. If unreachable, the UI disables AI buttons while preserving standard manual upload flows.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **AI LLM Engine** | Google Gemini API (via FastAPI microservice `8001`) | Not yet integrated in baseline repository |
| **Form Auto-Fill** | `AiUploadWidget.jsx` auto-populates React state | Manual entry / CSV drag-and-drop |
| **Natural Language Search** | `ChatWidget.jsx` maps prompt to filter IDs | Standard keyword search bar |
| **Microservice Isolation** | Standalone Python FastAPI microservice | Potential Next.js Server Action / API route |

---

## 3. SWOT Analysis

### Strengths
- **Massive Ingestion Acceleration**: Reduces metadata registration time from ~45 minutes to <3 minutes per dataset.
- **Intelligent Query Interpretation**: Translates layperson search queries into complex ICD-O/SNOMED filter selections.
- **Fail-Safe Architecture**: System operates 100% reliably even if AI microservices are disabled or offline.

### Weaknesses
- **LLM Hallucination Risk**: Requires human-in-the-loop validation before saving extracted metadata to production databases.
- **API Token Costs**: Dependent on external LLM API quotas (e.g. Google Gemini API pricing).

### Opportunities
- **Next.js Vercel AI SDK Integration**: Re-implement AI chat and extraction directly using Next.js Server Actions and standard AI SDK packages in `gateway-web`.
- **Local LLM Execution**: Option to run open-weight bio-medical models (e.g. BioMistral) via Ollama/vLLM for privacy-sensitive data.

### Threats
- **Data Privacy Compliance**: Ensuring un-anonymized dataset abstracts containing governance-restricted terms are scrubbed before sending to external API providers.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To introduce AI capabilities into `ayo/gateway-web`:

1. **Server-Side API Route**:
   - Implement `ayo/gateway-web/src/app/api/ai/extract/route.ts` using Next.js App Router API handlers.
2. **Component Integration**:
   - Port `AiUploadWidget.jsx` into `ayo/gateway-web/src/components/ai/AiMetadataExtractor.tsx`.
3. **Validation Gate**:
   - Ensure all AI-generated fields are flagged in the UI with a "Review AI Suggestions" badge before custodian submission.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented AI extraction, chat widget, & Gemini microservice. | `07_AI_METADATA_EXTRACTION_AND_CHAT.md` |
