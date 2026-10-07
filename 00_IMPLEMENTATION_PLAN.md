# CRUK Datahub Handover Documentation Implementation Plan

**Target Location**: `CRUK-handover-notes/`  
**Target Audience**: CRUK Technical Staff & Engineering Team  
**Context**: Handover from rapid prototype (`cruk_datahub_landing_page`) to production-ready version (`ayo/gateway-web`).

---

## 1. Overview & Objective

The objective of this documentation suite is to provide Cancer Research UK (CRUK) technical staff with a clear, comprehensive, and actionable handover of all system features built within the CRUK Datahub platform. 

The intended audience understands that:
- **`cruk_datahub_landing_page`** serves as the rapid-prototyping sandbox where features, user interface concepts, AST filtering engines, taxonomy representations, and AI workflows are actively designed, tested, and demonstrated.
- **`ayo/gateway-web`** (built on Next.js, React, Tailwind CSS, SWR, and i18next) represents the production-ready application framework designed for long-term deployment, scalability, and integration with the HDRUK Gateway API.

---

## 2. Standard Document Structure

To maintain consistency and ease of maintenance, each feature handover note follow a standardized 4-part layout:

1. **Feature Overview & Architecture**:
   - Technical description of the feature.
   - Core files, components, state management, and backend microservice interactions in `cruk_datahub_landing_page`.
2. **Prototype (`cruk_datahub_landing_page`) vs Production (`ayo/gateway-web`) Comparison**:
   - Detailed side-by-side technical audit.
   - What exists in the prototype vs what exists or is missing in `gateway-web`.
3. **SWOT Analysis**:
   - **Strengths**: Proven concepts, high UX responsiveness, custom capabilities.
   - **Weaknesses**: Prototype limitations (e.g. client-side memory overhead, mock data dependencies).
   - **Opportunities**: Architectural enhancements, backend acceleration, Next.js App Router integrations.
   - **Threats**: Technical debt, security vectors, schema divergence.
4. **Actionable Roadmap & Migration Recommendations**:
   - Step-by-step engineering guidance for porting/re-implementing the feature in `ayo/gateway-web`.
5. **Maintenance & Revision Log**:
   - Table for CRUK technical staff to record future updates, schema changes, or refactoring efforts.

---

## 3. Handover Documentation Suite Index

The documentation suite in `CRUK-handover-notes/` comprises the following files:

| File Name | Feature / Topic | Core Prototype Components | Gateway-Web Status |
| :--- | :--- | :--- | :--- |
| **`00_IMPLEMENTATION_PLAN.md`** | Handover Implementation Plan | Plan & Index | N/A |
| **`00_OVERVIEW.md`** | Ecosystem & System Architecture Overview | `README.md`, Multi-service stack | Microservice layout overview |
| **`01_BOOLEAN_SEARCH_ENGINE.md`** | AST Boolean Query & Logic Builder | `filterLogic.js`, `logic-utils.js`, `FilterApp.jsx` | Faceted search / API placeholder |
| **`02_TAXONOMY_TREE_AND_SEARCH.md`** | Hierarchical Cancer & Research Taxonomies | `longer_filter_data.js`, `flattened_filter_data.js` | Basic HDRUK filters |
| **`03_DATASET_CATALOGUE_AND_SCHEMA_VIEWER.md`** | Dataset Catalogue & Structural Metadata Grid | `DatasetsSection.jsx`, `StructuralMetadataGrid.jsx` | Standard dataset cards |
| **`04_TOOLS_PROJECTS_PUBLICATIONS.md`** | Tools, Projects & Publications Catalogues & Upload | `ToolUploadPage.jsx`, `ProjectMetadataPage.jsx` | Dialog placeholders |
| **`05_DATA_CUSTODIAN_AND_ACCESS_MANAGEMENT.md`** | Custodian Profiles & Access Request Workflows | `DataCustodian.jsx`, `TeamRequest.jsx` | DAR enquiry dialogs |
| **`06_ADMIN_HUB_AND_ANALYTICS.md`** | Administrative Hub, Error Logs & System Analytics | `ManageHub.jsx`, `ErrorLogsTab.jsx` | Basic team/account dialogs |
| **`07_AI_METADATA_EXTRACTION_AND_CHAT.md`** | AI Metadata Ingestion & Conversational Assistant | `AiUploadWidget.jsx`, `ChatWidget.jsx`, `ai-microservices` | Not yet integrated |
| **`08_INTERACTIVE_GUIDED_TOUR.md`** | Interactive Visual Onboarding & Tour Engine | `GuidedTourController.jsx`, `TourOverlay.jsx` | Not yet integrated |
| **`09_ACCESSIBILITY_AND_FEEDBACK.md`** | WCAG 2.1 AA Accessibility & Feedback Loop | WCAG aria-roles, focus-lock, `FeedbackModal.jsx` | `.pa11yci.js` standard |

---

## 4. Document Update & Maintenance Protocol

When technical staff make updates or introduce changes to either repository, they should update the corresponding handover note using the following guidelines:

1. **Update Revision History**: Add an entry to the document's revision table including Date, Author, Change Summary, and Affected Files.
2. **Update Status Labels**: Keep the "Gateway-Web Status" badge updated (e.g. `[STATUS: PROTOTYPE ONLY]` $\rightarrow$ `[STATUS: IN PROGRESS]` $\rightarrow$ `[STATUS: PRODUCTION READY]`).
3. **Reflect Code File Links**: Maintain clickable markdown file links (`file:///...`) pointing to relative or absolute repository locations.

---
*Created for CRUK Technical Handover by Sussex Bioinformatics Lab.*
