# Feature Note 05: Data Custodian Profiles & Access Request Workflows

**Target Audience**: CRUK Technical Staff & Governance Team  
**Module Location**: `cruk_datahub_landing_page/src/components/` & `ayo/gateway-web/src/modules/`  
**Status**: `[STATUS: PRODUCTION READY - HARMONIZED WITH GATEWAY DAR]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

Data governance and access control are critical when handling cancer research datasets. The CRUK Data Hub platform provides custodian management and streamlined Data Access Request (DAR) initiation.

```
┌──────────────────────────────────────┐       ┌──────────────────────────────────────┐
│ DataCustodians.jsx                   │       │ ContactCustodianModal.jsx            │
│ ───────────────────────────────────  │ ────► │ ───────────────────────────────────  │
│ Custodian Directory, Institution     │       │ Direct enquiry modal, access tier    │
│ profiles, governance badges          │       │ guidance, preliminary enquiries      │
└──────────────────────────────────────┘       └──────────────────────────────────────┘
                                                                  │
                                                                  ▼
                                               ┌──────────────────────────────────────┐
                                               │ TeamRequest.jsx / ManageTeamModal    │
                                               │ ───────────────────────────────────  │
                                               │ Team member application, dataset     │
                                               │ access request submission to middle  │
                                               └──────────────────────────────────────┘
```

### Key Subsystems & Components

1. **Custodian Directory & Profiles**:
   - **`DataCustodians.jsx` ([DataCustodians.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/DataCustodians.jsx))** & **`DataCustodian.jsx` ([DataCustodian.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/DataCustodian.jsx))**:
   - Lists data-holding organizations (e.g. CRUK Institutes, NHS Trusts, Universities).
   - Displays custodian contact details, data access committees (DAC), and institutional accreditation statuses.

2. **Custodian Contact & Governance**:
   - **`ContactCustodianModal.jsx` ([ContactCustodianModal.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ContactCustodianModal.jsx))**:
   - Provides researchers with an interactive modal to initiate preliminary feasibility enquiries directly with custodian representatives.

3. **Team Request & Access Authorization**:
   - **`TeamRequest.jsx` ([TeamRequest.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/TeamRequest.jsx))**:
   - Handles team creation, investigator identity verification, and dataset request routing to the `middle` layer administrative service (`TeamRequest` DB table).

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Custodian Profiles** | Standalone profiles (`DataCustodian.jsx`) with contact modals | Provider details dialog (`ProvidersDialog.tsx`) |
| **Feasibility Enquiries** | `ContactCustodianModal.jsx` modal form | `FeasibilityEnquiryDialog.tsx` & `DarEnquiryDialog.tsx` |
| **DAR Governance Engine** | Custom `TeamRequest` payload to `middle` layer proxy | Fully-fledged Five Safes DAR wizard (`DarManageDialog.tsx`) |
| **Data Use Register (DUR)** | Dataset card links & custodian badges | Dedicated Data Use Register module (`DataUseDetailsDialog`) |

---

## 3. SWOT Analysis

### Strengths
- **Low Friction Enquiries**: `ContactCustodianModal.jsx` allows researchers to check data feasibility before submitting lengthy formal DAR applications.
- **Institutional Clarity**: Custodian pages clearly outline access rules, ethics requirements, and DAC response SLAs.

### Weaknesses
- **Parallel Application Databases**: Prototype `middle` layer `TeamRequest` table operates independently of HDRUK Gateway DAR backend API.

### Opportunities
- **Harmonized DAR Flow**: Direct integration between custodian profile cards in `gateway-web` and HDRUK Gateway API 2.0 Five Safes DAR submission pipeline.
- **Automated Routing**: Automatically route access requests based on dataset sensitivity tags.

### Threats
- **Governance Bottlenecks**: Delays if custodian contact information becomes outdated in static UI files.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To harmonize this feature in `ayo/gateway-web`:

1. **Connect Custodian Metadata to Providers**:
   - Map `DataCustodian.jsx` fields into `ayo/gateway-web/src/modules/ProvidersDialog/`.
2. **Leverage Native Gateway DAR Modules**:
   - Connect the "Contact Custodian" CTA button directly to `FeasibilityEnquiryDialog.tsx` and `DarEnquiryDialog.tsx` in `gateway-web`.
3. **Data Use Register Alignment**:
   - Sync approved team access decisions from `TeamRequest` into `gateway-web`'s `DataUseDetailsDialog`.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented custodian profiles & access request governance workflows. | `05_DATA_CUSTODIAN_AND_ACCESS_MANAGEMENT.md` |
