# CRUK Data Hub Platform — Handover Overview

**Target Audience**: CRUK Technical Staff & Engineering Team  
**Status**: Handover Reference Document  
**Last Updated**: 2026-10-07  

---

## 1. System Philosophy: Prototype vs. Production

The CRUK Data Hub repository ecosystem was developed using a deliberate dual-system strategy to accelerate feature discovery while ensuring robust production delivery:

```
                  ┌─────────────────────────────────────────────────────────┐
                  │                 CRUK DATA HUB ECOSYSTEM                 │
                  └─────────────────────────────────────────────────────────┘
                                       │
            ┌──────────────────────────┴──────────────────────────┐
            ▼                                                     ▼
┌──────────────────────────────────────┐               ┌──────────────────────────────────────┐
│  cruk_datahub_landing_page (Vite)    │               │       ayo/gateway-web (Next.js)      │
│  ──────────────────────────────────  │               │       ─────────────────────────      │
│  • Rapid Prototyping Sandbox         │               │  • Production-Ready Application      │
│  • Experimental UX & Query Engine    │               │  • SSR / SSG & App Router Architecture│
│  • Standalone Client-Side Execution  │               │  • HDRUK Gateway API 2.0 Integration │
│  • AI & AST Interactive Prototypes   │               │  • Enterprise i18n & Pa11y Testing   │
└──────────────────────────────────────┘               └──────────────────────────────────────┘
```

1. **`cruk_datahub_landing_page` (Prototype Sandbox)**:
   - **Role**: A React + Vite Multi-Page Application (MPA) serving as the experimental UI, user feedback mechanism, and technical sandbox.
   - **Key Innovations**: Client-side AST Boolean logic evaluator, hierarchical CRUK/ICD-O taxonomy viewer, interactive SVG guided tour overlay, Google Gemini AI extraction widget, and live schema form editor.
   - **Architecture**: Modular Vite build mounting lightweight HTML entry points (`/datasets.html`, `/upload.html`, `/dashboard.html`) interacting with python microservices (`basic`, `middle`, `ai`).

2. **`ayo/gateway-web` (Production Platform)**:
   - **Role**: A Next.js App Router application built by Sussex Bioinformatics Lab in collaboration with HDRUK.
   - **Key Characteristics**: Server-Side Rendering (SSR), SWR caching layer, i18next multi-language support, automated accessibility testing (`pa11y`), and direct integration with the HDRUK Gateway API 2.0.

---

## 2. Microservice Stack Architecture

The prototype ecosystem operates via 5 interconnected backend repositories:

| Microservice | Location / Repository | Technology Stack | Port | Primary Responsibility |
| :--- | :--- | :--- | :--- | :--- |
| **Landing Page UI** | `cruk_datahub_landing_page` | React 18, Vite, Tailwind CSS | `5173` | Core dataset catalogue, search, filters, guided tour, metadata forms. |
| **Basic Backend** | `basic/basic_backend` | FastAPI, SQLite, Pydantic | `8000` | User auth, team management, dataset metadata storage, draft state workflows. |
| **Middle Layer Proxy** | `middle` | FastAPI, SQLite, HTTPX | `8002` | Administrative team requests, system error logs, middleware proxying. |
| **Semantic Schema Viewer** | `semantic-schema/cruk-semantic-schema` | React, JSON Schema Validator | Shared | Standalone viewer and schema provider for CRUK 1.0.0 & HDRUK 4.0.0 definitions. |
| **AI Microservices** | `ai/ai-microservices` | FastAPI, Google Gemini API | `8001` | Automated metadata extraction, natural language query parsing, chat widget back-end. |

---

## 3. Core Feature Matrix & Production Readiness Summary

The table below summarizes all 9 major functional domains built in `cruk_datahub_landing_page` and their corresponding status in `ayo/gateway-web`:

| Feature Area | Prototype Implementation | Production Status in `gateway-web` | Migration Priority | Handover Document Link |
| :--- | :--- | :--- | :--- | :--- |
| **1. AST Boolean Search Engine** | Client-side AST evaluator (`filterLogic.js`, `logic-utils.js`) | API faceted search / basic text search | 🔴 High | [01_BOOLEAN_SEARCH_ENGINE.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/01_BOOLEAN_SEARCH_ENGINE.md) |
| **2. Cancer Taxonomy Tree** | ICD-O, SNOMED, TCGA, CRUK tree & $O(1)$ dictionary | Standard HDRUK filters | 🔴 High | [02_TAXONOMY_TREE_AND_SEARCH.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/02_TAXONOMY_TREE_AND_SEARCH.md) |
| **3. Dataset Catalogue & Schema Viewer** | Structural grid editor & CRUK 1.0.0 overlay | Standard HDRUK dataset card views | 🟡 Medium | [03_DATASET_CATALOGUE_AND_SCHEMA_VIEWER.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/03_DATASET_CATALOGUE_AND_SCHEMA_VIEWER.md) |
| **4. Tools, Projects & Publications** | Multi-entity catalogues & dedicated upload forms | Dialog modals / route placeholders | 🟡 Medium | [04_TOOLS_PROJECTS_PUBLICATIONS.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/04_TOOLS_PROJECTS_PUBLICATIONS.md) |
| **5. Custodian & Access Management** | Custodian directory & direct team access requests | Robust DAR enquiry & feasibility dialogs | 🟢 Integrated | [05_DATA_CUSTODIAN_AND_ACCESS_MANAGEMENT.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/05_DATA_CUSTODIAN_AND_ACCESS_MANAGEMENT.md) |
| **6. Admin Hub & Error Analytics** | `ManageHub.jsx` dashboard & error logs tab | `cruk-admin` repo / basic account nav | 🟡 Medium | [06_ADMIN_HUB_AND_ANALYTICS.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/06_ADMIN_HUB_AND_ANALYTICS.md) |
| **7. AI Extraction & Chat** | Gemini-powered file tagging & chat assistant | Not yet integrated | 🟢 Optional | [07_AI_METADATA_EXTRACTION_AND_CHAT.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/07_AI_METADATA_EXTRACTION_AND_CHAT.md) |
| **8. Interactive Guided Tour** | SVG spotlight mask & step controller | Not yet integrated | 🟡 Medium | [08_INTERACTIVE_GUIDED_TOUR.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/08_INTERACTIVE_GUIDED_TOUR.md) |
| **9. Accessibility & Feedback** | WCAG 2.1 AA compliant UI & feedback modal | `.pa11yci.js` & standard Next.js a11y | 🟢 Complete | [09_ACCESSIBILITY_AND_FEEDBACK.md](file:///Users/skw24/CRUK/website/CRUK-handover-notes/09_ACCESSIBILITY_AND_FEEDBACK.md) |

---

## 4. Maintenance & Revision History

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Initial creation of CRUK Handover Overview document. | `00_OVERVIEW.md` |
