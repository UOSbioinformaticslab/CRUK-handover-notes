# Feature Note 03: Dataset Catalogue, Metadata Viewer & Structural Metadata Grid

**Target Audience**: CRUK Technical Staff & Frontend Developers  
**Module Location**: `cruk_datahub_landing_page/src/components/`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - ADVANCED GRID READY]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

The Dataset Catalogue and Schema Viewing subsystem provides dataset discovery, detailed structural metadata inspection, and interactive schema validation.

```
┌──────────────────────────────────────┐       ┌──────────────────────────────────────┐
│ DatasetsSection.jsx                  │       │ MetaDataPage.jsx / SchemaPage.jsx    │
│ ───────────────────────────────────  │ ────► │ ───────────────────────────────────  │
│ Catalogue grid view, cards, tags,    │       │ In-depth metadata tabs, summary,     │
│ access badges, filter event listener │       │ CRUK 1.0.0 dynamic schema overlay    │
└──────────────────────────────────────┘       └──────────────────────────────────────┘
                                                                  │
                                                                  ▼
                                               ┌──────────────────────────────────────┐
                                               │ StructuralMetadataGrid.jsx           │
                                               │ ───────────────────────────────────  │
                                               │ Dynamic table & column grid viewer,  │
                                               │ CSV/JSON ingestion via flattenSchema │
                                               └──────────────────────────────────────┘
```

### Key Subsystems & Components

1. **`DatasetsSection.jsx` ([DatasetsSection.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/DatasetsSection.jsx))**:
   - List and grid view of all registered cancer datasets.
   - Listens to global `apply-dataset-filters` events dispatched by `FilterApp.jsx` to update matching dataset cards.
   - Displays access restriction badges, multi-omic tag pills, data custodian links, and structural summary metadata.

2. **`MetaDataPage.jsx` ([MetaDataPage.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/MetaDataPage.jsx)) & `SchemaPage.jsx` ([SchemaPage.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/SchemaPage.jsx))**:
   - Comprehensive metadata viewer for individual datasets.
   - Displays abstract, provenance, sample sizes, licensing, contact custodian CTA, and live schema documentation.
   - Integrates the `cruk-semantic-schema` package to render CRUK 1.0.0 and HDRUK 4.0.0 compliant metadata overlays dynamically.

3. **`StructuralMetadataGrid.jsx` ([StructuralMetadataGrid.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/StructuralMetadataGrid.jsx))**:
   - Specialized interactive data grid for inspecting dataset tables, columns, data types, descriptions, and sensitive data flags.
   - Powered by `flattenSchemaToGrid.js` to convert nested JSON schema structures into flat, sortable grid rows.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Dataset Detail Cards** | Custom CRUK branding, multi-omic pill tags, quick access | HDRUK standard dataset cards |
| **Schema Rendering** | Interactive `StructuralMetadataGrid` & CRUK 1.0.0 overlay | Standard HDRUK metadata schema display |
| **Grid Manipulation** | Client-side CSV upload & flat grid transformation | API payload rendering |
| **Event Synchronization** | Custom DOM event listener (`apply-dataset-filters`) | SWR state & React hook propagation |

---

## 3. SWOT Analysis

### Strengths
- **Granular Transparency**: Structural metadata grid allows researchers to evaluate specific table columns before applying for data access.
- **Dynamic Schema Overlay**: `cruk-semantic-schema` guarantees compliance with both HDRUK 4.0.0 and CRUK 1.0.0 standards.
- **Bulk CSV Ingestion**: Custodians can preview structural schemas by uploading raw CSV data dictionaries.

### Weaknesses
- **CSS Grid Truncation**: Large data tables with 100+ columns require dynamic virtualization to avoid DOM clutter.

### Opportunities
- **Component Porting**: Package `StructuralMetadataGrid.jsx` as a shared React component library for inclusion in `ayo/gateway-web`.
- **Export Capabilities**: Allow researchers to export structural table column metadata as Data Use Register (DUR) attachments.

### Threats
- **Schema Format Drift**: Mismatch between HDRUK schema updates and local CRUK 1.0.0 overlay schema definitions.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To integrate into `ayo/gateway-web`:

1. **Extract `StructuralMetadataGrid` Component**:
   - Move `StructuralMetadataGrid.jsx` and `flattenSchemaToGrid.js` into `ayo/gateway-web/src/components/datasets/StructuralMetadataGrid.tsx`.
2. **Next.js Page Route**:
   - Create route `ayo/gateway-web/src/app/[locale]/datasets/[id]/page.tsx` for dataset detail view.
3. **Integrate CRUK Semantic Schema**:
   - Import `cruk-semantic-schema` dependency in `gateway-web/package.json` to handle dynamic metadata rendering.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented dataset catalogue, schema page, & structural metadata grid. | `03_DATASET_CATALOGUE_AND_SCHEMA_VIEWER.md` |
