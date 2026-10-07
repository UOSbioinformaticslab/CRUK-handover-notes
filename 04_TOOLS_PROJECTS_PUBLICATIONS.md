# Feature Note 04: Software Tools, Research Projects & Publications Workflows

**Target Audience**: CRUK Technical Staff & Application Developers  
**Module Location**: `cruk_datahub_landing_page/src/components/`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - MULTI-ENTITY READY]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

Beyond raw dataset metadata, the CRUK Data Hub platform catalogues **Software Tools**, **Research Projects**, and **Academic Publications** linked to cancer datasets.

```
                                ┌─────────────────────────────────────────┐
                                │     CRUK DATAHUB MULTI-ENTITY CATALOG   │
                                └─────────────────────────────────────────┘
                                                     │
       ┌─────────────────────────────────────────────┼─────────────────────────────────────────────┐
       ▼                                             ▼                                             ▼
┌──────────────────────────────┐              ┌──────────────────────────────┐              ┌──────────────────────────────┐
│       SOFTWARE TOOLS         │              │      RESEARCH PROJECTS       │              │    ACADEMIC PUBLICATIONS     │
│ ──────────────────────────── │              │ ──────────────────────────── │              │ ──────────────────────────── │
│ • ToolPage.jsx / ToolList    │              │ • ProjectsSection.jsx        │              │ • PublicationDashboard.jsx   │
│ • ToolUploadPage.jsx         │              │ • ProjectMetadataPage.jsx    │              │ • PublicationUpload.jsx      │
│ • Analytical pipelines, code │              │ • ProjectGrantSchemaPage.jsx │              │ • EditPublicationModal.jsx   │
│   repositories & licenses    │              │ • CRUK Grant IDs & Cohorts   │              │ • DOI lookup & PDF metadata  │
└──────────────────────────────┘              └──────────────────────────────┘              └──────────────────────────────┘
```

### Key Subsystems & Components

1. **Software Tools Catalogue & Registration**:
   - **`ToolPage.jsx` ([ToolPage.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ToolPage.jsx))** & **`ToolUploadPage.jsx` ([ToolUploadPage.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ToolUploadPage.jsx))**:
   - Catalogues bioinformatic workflows, analysis pipelines, containers (Docker/Singularity), and scripts.
   - Captures repository links (GitHub/GitLab), programming languages, documentation URLs, and licensing terms.

2. **Research Projects Directory**:
   - **`ProjectsSection.jsx` ([ProjectsSection.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ProjectsSection.jsx))** & **`ProjectMetadataPage.jsx` ([ProjectMetadataPage.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ProjectMetadataPage.jsx))**:
   - Links CRUK funding grant IDs, lead investigators, participating research institutions, and target cancer cohorts.

3. **Academic Publications Workflows**:
   - **`PublicationDashboard.jsx` ([PublicationDashboard.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/PublicationDashboard.jsx))** & **`PublicationUpload.jsx` ([PublicationUpload.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/PublicationUpload.jsx))**:
   - Direct DOI ingestion, citation metadata formatting, journal links, and associated dataset cross-references.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Dedicated Catalog Pages** | Separate MPA pages (`tools.html`, `projects.html`, `publications.html`) | Consolidated routing under Next.js App Router |
| **Entity Ingestion Forms** | Full multi-step upload forms (`ToolUploadPage`, `PublicationUpload`) | Dialog modal placeholders (`AddPublicationDialog`, `ToolDetailsDialog`) |
| **Backend Storage** | FastAPI `basic_backend` tables (`/tools`, `/projects`, `/publications`) | HDRUK Gateway API 2.0 endpoints |
| **Dataset Cross-Linking** | Interactive dropdown selection linking tools/pubs to dataset IDs | Gateway resource relation dialogs |

---

## 3. SWOT Analysis

### Strengths
- **Comprehensive Ecosystem**: Captures the full research lifecycle from raw data to software tools and published paper.
- **Grant Validation**: Project metadata supports direct linking to CRUK grant reference numbers.
- **Dedicated Tool Metadata**: Detailed schema for bioinformatics tools (Docker containers, execution environments).

### Weaknesses
- **Form Duplication**: Similar metadata fields across dataset, tool, and project upload forms can lead to UI redundancy.

### Opportunities
- **Automated DOI Lookup**: Integrate CrossRef / PubMed API into `PublicationUpload` to auto-populate metadata from DOIs.
- **App Router Integration**: Move standalone HTML upload pages into unified Next.js App Router routes in `gateway-web`.

### Threats
- **Orphaned Entities**: Software tools or publications saved without explicit links to parent datasets.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To port these entity workflows into `ayo/gateway-web`:

1. **Next.js App Router Routes**:
   - Create route directories:
     - `ayo/gateway-web/src/app/[locale]/tools/page.tsx`
     - `ayo/gateway-web/src/app/[locale]/projects/page.tsx`
     - `ayo/gateway-web/src/app/[locale]/publications/page.tsx`
2. **Modal dialog to Page Form Transition**:
   - Expand `AddPublicationDialog.tsx` into a full-page metadata submission flow utilizing Next.js React Hook Form and Zod schema validation.
3. **Gateway API 2.0 Endpoint Binding**:
   - Update `ayo/gateway-web/src/services/` with SWR fetchers for `/tools`, `/projects`, and `/publications`.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented tools, projects, & publications catalogue & upload workflows. | `04_TOOLS_PROJECTS_PUBLICATIONS.md` |
