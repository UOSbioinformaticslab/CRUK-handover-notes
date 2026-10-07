# Feature Note 08: Interactive Guided Tour & Visual Onboarding Engine

**Target Audience**: CRUK Technical Staff & UX Developers  
**Module Location**: `cruk_datahub_landing_page/src/components/guided_tour/` & `cruk_datahub_landing_page/tourguide.md`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - DECOUPLED FRAMEWORK]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

The Interactive Guided Tour engine provides step-by-step visual onboarding walkthroughs across the platform. It spotlights targeted UI elements with SVG cutout masks, displays contextual thought bubbles, and automatically manipulates application state (opening accordions, checking taxonomy items, toggling Boolean operators).

```
┌──────────────────────────────────────┐       ┌──────────────────────────────────────┐
│ Header.jsx (Navbar)                  │       │ GuidedTourController.jsx             │
│ ───────────────────────────────────  │ ────► │ ───────────────────────────────────  │
│ Global "Guided Tour ▼" dropdown menu │       │ Event listener for 'startGuidedTour' │
│ Dispatches CustomEvent               │       │ Manages step index & state setters   │
└──────────────────────────────────────┘       └──────────────────────────────────────┘
                                                                  │
                                                                  ▼
┌──────────────────────────────────────┐       ┌──────────────────────────────────────┐
│ TourOverlay.jsx                      │ ◄──── │ Step Configs (simpleFilters.js /    │
│ ───────────────────────────────────  │       │               advancedFilters.js)    │
│ Renders SVG spotlight mask cutout    │       │ Step targets, titles, descriptions,  │
│ & relative popover thought bubble    │       │ and DOM state manipulation handlers  │
└──────────────────────────────────────┘       └──────────────────────────────────────┘
```

### Key Subsystems & Components

1. **`GuidedTourController.jsx` ([GuidedTourController.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/guided_tour/GuidedTourController.jsx))**:
   - Central state machine controlling active tour state, current step index, and transitions.
   - Cross-page navigation awareness: Reads `sessionStorage.getItem('pendingGuidedTour')`. If initiated from non-catalog pages (e.g. `about.html`), it routes the user to `dashboard.html` to highlight search entry points before resuming filter steps on `datasets.html`.

2. **`TourOverlay.jsx` ([TourOverlay.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/guided_tour/TourOverlay.jsx))**:
   - Visual presentation layer using SVG path cutouts with `rgba(15, 23, 42, 0.75)` translucency.
   - Computes target element geometry dynamically via `getBoundingClientRect()` to ensure responsive positioning across window resizes.

3. **Step Configurations (`simpleFilters.js` / `advancedFilters.js`)**:
   - Decoupled step definitions defining `target` DOM selectors (`[data-tour="..."]`), step `title`, `content`, and `onEnter()` state triggers.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Spotlight Rendering** | Custom SVG path cutout overlay (`TourOverlay.jsx`) | Not yet implemented in baseline repository |
| **Cross-Page Routing** | `sessionStorage` state persistence across HTML pages | Next.js App Router state / `next/navigation` |
| **State Automation** | Direct callback handlers (`onEnter()`) opening React accordions | Potential React Context state machine |
| **Decoupled Step Config** | Standalone JavaScript config files | TypeScript step definition arrays |

---

## 3. SWOT Analysis

### Strengths
- **Decoupled Architecture**: Adding a new tour requires creating a step config file without modifying core component rendering code.
- **Zero Third-Party Dependencies**: Pure React & SVG implementation avoids heavy external tour library dependencies.
- **Cross-Page Persistence**: Seamlessly guides users through page transitions.

### Weaknesses
- **DOM Selector Coupling**: Step configurations rely on stable `data-tour="..."` attributes on DOM elements.

### Opportunities
- **Reusable React Component**: Package `GuidedTourController` and `TourOverlay` as a shared UI component for `ayo/gateway-web`.
- **Analytics Tracking**: Log tour completions to analyze researcher onboarding success.

### Threats
- **Refactoring Breakage**: Renaming DOM element selectors during UI redesigns can break tour step spotlight targets if `data-tour` tags are removed.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To introduce guided onboarding into `ayo/gateway-web`:

1. **Port Tour Components**:
   - Copy `GuidedTourController.jsx` and `TourOverlay.jsx` to `ayo/gateway-web/src/components/tour/`.
2. **Standardize Target Tags**:
   - Ensure critical Next.js components in `gateway-web` (search bar, filter accordion, DAR CTA button) include `data-tour` attributes.
3. **Next.js Navigation Integration**:
   - Replace `window.location.href` transitions in `GuidedTourController` with Next.js `router.push()` from `next/navigation`.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented guided tour controller, SVG overlay, & step configurations. | `08_INTERACTIVE_GUIDED_TOUR.md` |
