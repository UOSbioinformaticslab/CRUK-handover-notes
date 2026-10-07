# Feature Note 09: WCAG 2.1 AA Accessibility Infrastructure & User Feedback Loop

**Target Audience**: CRUK Technical Staff, Frontend Engineers & A11y Auditors  
**Module Location**: `cruk_datahub_landing_page/src/components/` & `ayo/gateway-web/.pa11yci.js`  
**Status**: `[STATUS: PRODUCTION READY - WCAG 2.1 AA COMPLIANT]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

Accessibility (a11y) and user feedback mechanisms ensure the CRUK Data Hub platform is usable by researchers of all abilities and provides continuous user insight to developers.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 ACCESSIBILITY (WCAG 2.1 AA) INFRASTRUCTURE                  │
└─────────────────────────────────────────────────────────────────────────────┘
                                       │
        ┌──────────────────────────────┼──────────────────────────────┐
        ▼                              ▼                              ▼
┌──────────────────────────────┐┌──────────────────────────────┐┌──────────────────────────────┐
│  SEMANTIC HTML & LANDMARKS   ││ INTERACTIVE ELEMENTS & ARIA  ││   KEYBOARD & FOCUS TRAP      │
│  ──────────────────────────  ││ ───────────────────────────  ││   ─────────────────────      │
│  • <html lang="en"> & <title>││ • Native <button> elements   ││ • react-focus-lock modal     │
│  • Single <h1> per page      ││ • aria-expanded attributes   ││   trapping in modals         │
│  • <main> & <aside> roles    ││ • Unique label/input IDs     ││ • Escape key & blur handlers │
└──────────────────────────────┘└──────────────────────────────┘└──────────────────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       USER FEEDBACK LOOP SUBSYSTEM                          │
│ ─────────────────────────────────────────────────────────────────────────── │
│ • FeedbackWidget.jsx: Floating sticky feedback trigger icon                 │
│ • FeedbackModal.jsx: Categorized feedback collection modal                  │
│ • User ratings, feature bug reports, and UX recommendations                 │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Accessibility Remediations & Technical Patterns

1. **Global Semantics & Heading Structure**:
   - Every entry point provides explicit `<html lang="en">`, descriptive `<title>` tags, and a strict single `<h1>` heading hierarchy.

2. **Dynamic Form Linkage & Unique Field IDs**:
   - Dynamic schema forms generate unique field IDs (e.g. `id="field-summary-title"`) paired with explicit `<label htmlFor="...">` attributes for screen readers.

3. **Operable Controls & ARIA Landmarks**:
   - Collapsible tree nodes and category accordions use native `<button type="button">` elements with keyboard `Tab` / `Enter` support and dynamic `aria-expanded` states.
   - Tab-based navigation in administrative modules (`ManageHub.jsx`) implements full `role="tablist"` and `role="tab"` standards.

4. **Focus Trapping & Keyboard Operability**:
   - Modals (`FeedbackModal.jsx`, `SignInModal.jsx`, `ManageTeamModal.jsx`) incorporate `react-focus-lock` to prevent keyboard focus from bleeding into background DOM trees.
   - Dropdown menus close on `Escape` keypress and focus `Blur`.

5. **User Feedback Collection**:
   - **`FeedbackWidget.jsx` ([FeedbackWidget.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/FeedbackWidget.jsx))** & **`FeedbackModal.jsx` ([FeedbackModal.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/FeedbackModal.jsx))**:
   - Enables users to submit structured feedback ratings and bug reports.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **A11y Audit Method** | Manual WCAG 2.1 AA remediation audit | Automated CI accessibility testing via `.pa11yci.js` |
| **Focus Trapping** | `react-focus-lock` library in modals | Next.js Radix UI / Headless UI focus traps |
| **Feedback Mechanism** | `FeedbackWidget.jsx` & `FeedbackModal.jsx` | Gateway feedback dialog / issue tracker link |
| **Multi-Language (i18n)** | Static English content | `i18next` localized screen reader labels |

---

## 3. SWOT Analysis

### Strengths
- **Fully Compliant**: Meets WCAG 2.1 AA accessibility standards for public sector and academic research mandates.
- **Screen Reader Friendly**: Guaranteed unique ID-to-label bindings across dynamic schema forms.
- **Embedded Feedback Loop**: In-app feedback collection helps developers prioritize high-value user improvements.

### Weaknesses
- **Manual Verification Needed**: Visual color contrast on custom theme variants requires periodic re-auditing.

### Opportunities
- **Automated CI Integration**: Incorporate `.pa11yci.js` configurations from `gateway-web` into the prototype build process.
- **Centralized Feedback Queue**: Route submitted feedback directly into GitHub Issues or Jira via `middle` proxy hooks.

### Threats
- **Third-Party Styling Overrides**: Adding unvetted external CSS styles that diminish focus indicator outlines.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To maintain accessibility excellence in `ayo/gateway-web`:

1. **Leverage Existing Pa11y Test Runner**:
   - Run `npm run test:pa11y` in `ayo/gateway-web` to verify continuous WCAG compliance.
2. **Standardize Modal Focus Locking**:
   - Utilize Next.js accessible dialog primitives (`@radix-ui/react-dialog`) across all modals.
3. **Harmonize Feedback API**:
   - Route `FeedbackModal.jsx` submissions to the production feedback endpoint configured in `gateway-web`.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented WCAG 2.1 AA accessibility infrastructure & feedback loop. | `09_ACCESSIBILITY_AND_FEEDBACK.md` |
