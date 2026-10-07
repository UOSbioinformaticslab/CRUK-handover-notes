# Feature Note 02: Hierarchical Cancer Taxonomies & $O(1)$ Fast Lookup Engine

**Target Audience**: CRUK Technical Staff & Bioinformaticians  
**Module Location**: `cruk_datahub_landing_page/src/utils/` & `cruk_datahub_landing_page/src/components/FilterApp.jsx`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - TAXONOMY READY]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

Cancer research metadata requires domain-specific terminology classification. The CRUK Data Hub taxonomy engine organizes and exposes complex hierarchical medical ontologies to researchers in an intuitive, multi-level tree interface.

### Supported Terminology Systems

1. **ICD-O-3 (International Classification of Diseases for Oncology)**:
   - **Topography**: Anatomical site coding (e.g., `C50` Breast, `C18` Colon).
   - **Histology**: Morphological cell types (e.g., `8500/3` Infiltrating duct carcinoma).
2. **SNOMED-CT**: Clinical terminology codes mapped to research domains.
3. **TCGA (The Cancer Genome Atlas)**: Standardized cohort identifiers (e.g., `BRCA`, `LUAD`, `COAD`).
4. **CRUK Specific Cancer Terms**: Layperson-friendly and high-level categorization (e.g., "Bowel cancer", "Brain tumours").
5. **Data Types & Access Restrictions**: Multi-omic tags (Genomics, Epigenomics, Imaging) and access tiers (Open, Accredited, Ethics Required).

```
   RAW TAXONOMY JSON                           INITIALIZATION                                  RUNTIME LOOKUP
┌───────────────────────┐             ┌───────────────────────────────┐             ┌───────────────────────────┐
│ longer_filter_data.js │ ──────────► │ filter-setup.js               │ ──────────► │ filterDetailsMap.get(id)  │
│ (Hierarchical Tree)   │             │ (Flattens tree into ES6 Map)  │             │ O(1) Instant Label & Icon │
└───────────────────────┘             └───────────────────────────────┘             └───────────────────────────┘
```

### Key Subsystems & Files

- **`src/utils/longer_filter_data.js` ([longer_filter_data.js](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/utils/longer_filter_data.js))**:
  - The single source of truth for raw hierarchical ontology trees.
- **`src/utils/flattened_filter_data.js` ([flattened_filter_data.js](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/utils/flattened_filter_data.js))**:
  - Pre-computed flat dictionary mapping every filter ID (e.g. `0_0_2_1`) directly to label, category, parent node, and icon prefix.
- **`src/utils/filter-setup.js` ([filter-setup.js](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/utils/filter-setup.js))**:
  - Instantiates `filterDetailsMap` as a global JavaScript `Map` instance during app bootstrap, guaranteeing $O(1)$ constant-time lookup performance during UI render cycles.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Ontology Scope** | Deep ICD-O Topography/Histology, TCGA, SNOMED & CRUK terms | Standard HDRUK high-level filters |
| **Lookup Strategy** | Local client-side $O(1)$ `Map` pre-loaded in browser memory | Server-driven SWR fetch / backend REST taxonomy queries |
| **Tree Traversal UI** | Expandable accordions with instant node state propagation | Basic nested select / checkbox lists |
| **Parent-Child Expansion** | Automatic parent key resolution (`calculateLogicTokens`) | Explicit item selections |

---

## 3. SWOT Analysis

### Strengths
- **Domain Precision**: Full support for ICD-O and TCGA allows specialized cancer researchers to find relevant datasets immediately.
- **Instant Responsiveness**: $O(1)$ local `Map` lookups eliminate spinner delays while expanding taxonomy trees.
- **Hierarchical Intelligence**: Selecting a child node automatically retains parent context.

### Weaknesses
- **Bundle Size**: Large taxonomy JSON trees embedded in client bundle increase initial JavaScript parse time.
- **Static Synchronicities**: Requires build step or bundle update when ontology definitions change.

### Opportunities
- **Ontology API Integration**: Serve taxonomies dynamically via FastAPI backend or gateway-web API endpoints with SWR caching.
- **Semantic Tagging**: Use taxonomy IDs to automatically tag uploaded dataset abstracts via AI (`AiUploadWidget`).

### Threats
- **Versioning Divergence**: Discrepancies between HDRUK standard taxonomies and CRUK custom cancer categories if not synchronized regularly.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To migrate the taxonomy engine into `ayo/gateway-web`:

1. **Taxonomy Configuration Sharing**:
   - Move `longer_filter_data.js` and `flattened_filter_data.js` into `CRUK-taxonomies/` or an npm package `@cruk/taxonomies`.
2. **Next.js Hook Integration**:
   - Create custom hook `useCrukTaxonomies()` inside `ayo/gateway-web/src/hooks/useCrukTaxonomies.ts`.
   - Leverage Next.js Static Site Generation (SSG) or SWR caching to serve taxonomy dictionaries without bundling large JSON blobs into client JS.
3. **Filter Accordion Component**:
   - Port the expandable tree accordion from `FilterApp.jsx` into `ayo/gateway-web/src/components/taxonomies/TaxonomyTreeSelect.tsx`.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented hierarchical cancer taxonomies & $O(1)$ lookup Map implementation. | `02_TAXONOMY_TREE_AND_SEARCH.md` |
