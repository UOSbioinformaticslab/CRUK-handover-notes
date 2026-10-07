# Feature Note 06: Administrative Hub, Error Logging & System Analytics

**Target Audience**: CRUK Technical Staff & System Administrators  
**Module Location**: `cruk_datahub_landing_page/src/components/` & `middle/`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - MIDDLEWARE INTEGRATED]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

The Administrative Hub provides system administrators and hub managers with centralized control over dataset approvals, team memberships, error log monitoring, and platform usage analytics.

```
                                  ┌─────────────────────────────────────────┐
                                  │      ManageHub.jsx (Admin Dashboard)    │
                                  └─────────────────────────────────────────┘
                                                       │
         ┌──────────────────────┬──────────────────────┼──────────────────────┬──────────────────────┐
         ▼                      ▼                      ▼                      ▼                      ▼
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│ DATASETS TAB     │  │ TEAMS & USERS    │  │ ERROR LOGS TAB   │  │ ANALYTICS TAB    │  │ INVITATIONS TAB  │
│ ──────────────── │  │ ──────────────── │  │ ──────────────── │  │ ──────────────── │  │ ──────────────── │
│ Draft/Publish    │  │ ManageTeamModal  │  │ ErrorLogsTab.jsx │  │ AdminAnalytics   │  │ InvitationsModal │
│ Approval & Delete│  │ ChangePassword   │  │ Live proxy log   │  │ Traffic & search │  │ Pending invites  │
│ workflows        │  │ modal            │  │ inspection       │  │ metrics          │  │ & user roles     │
└──────────────────┘  └──────────────────┘  └──────────────────┘  └──────────────────┘  └──────────────────┘
```

### Key Subsystems & Components

1. **`ManageHub.jsx` ([ManageHub.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ManageHub.jsx))**:
   - Tabbed administrative interface (`role="tablist"` and `role="tab"` standard compliance).
   - Manages approval transitions for submitted datasets (`draft` $\rightarrow$ `published`).

2. **Error Logging Tab (`ErrorLogsTab.jsx` ([ErrorLogsTab.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ErrorLogsTab.jsx)))**:
   - Directly fetches system exception tracebacks and API errors from the `middle` proxy service (`http://localhost:8002/error-logs`).
   - Supports status filtering, search by endpoint/error code, and clear log actions.

3. **Analytics Dashboard (`AdminAnalyticsTab.jsx` ([AdminAnalyticsTab.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/AdminAnalyticsTab.jsx)))**:
   - Displays aggregated platform metrics: search query frequencies, dataset view counts, custodian contact events, and active user trends.

4. **Team & Invitation Management**:
   - **`ManageTeamModal.jsx` ([ManageTeamModal.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/ManageTeamModal.jsx))** & **`InvitationsModal.jsx` ([InvitationsModal.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/InvitationsModal.jsx))**:
   - Manages team member roles (Admin, Editor, Viewer) and generates secure email invitation tokens.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Admin UI Location** | Integrated tabbed view (`manage_hub.html`) | Separate repository (`cruk-admin`) & `AccountNav` |
| **Error Monitoring** | Custom FastAPI `middle` proxy logger (`middlelayer.db`) | Enterprise Sentry integration (`sentry.server.config.ts`) |
| **User Role Control** | Local JWT auth + FastAPI `basic_backend` admin flags | Auth0 / Keycloak / Gateway OAuth2 provider |
| **Analytics Engine** | `AdminAnalyticsTab.jsx` SQLite aggregation | Telemetry / SWR & Gateway API analytics |

---

## 3. SWOT Analysis

### Strengths
- **All-in-One Management**: Administrators can review pending datasets, resolve user issues, and inspect error logs in a single UI.
- **Dedicated Error Inspector**: `ErrorLogsTab.jsx` provides instant diagnostic insight without needing access to server terminal logs.

### Weaknesses
- **Monolithic Component Size**: `ManageHub.jsx` (~53KB) and `ErrorLogsTab.jsx` (~20KB) are heavy and benefit from further modularization.

### Opportunities
- **Sentry Integration Sync**: Forward `middle` layer proxy logs into `sentry.server.config.ts` in `gateway-web`.
- **Role-Based Access Control (RBAC)**: Enforce granular admin roles directly via Next.js middleware (`proxy.ts`).

### Threats
- **Unauthenticated API Endpoints**: Ensure admin routes on `middle` proxy (`8002`) enforce strict Bearer token authentication in production.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To unify administration in `ayo/gateway-web`:

1. **Adopt Sentry for Error Logging**:
   - Rely on `ayo/gateway-web/src/sentry.server.config.ts` for automated runtime error capture.
2. **Modularize Admin Tabs**:
   - Port `AdminAnalyticsTab.jsx` into `ayo/gateway-web/src/app/[locale]/account/analytics/page.tsx`.
   - Port team invitation dialogs into `ayo/gateway-web/src/modules/DeleteTeamDialog/` and related team management modules.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented administrative hub, error logging tab, and analytics. | `06_ADMIN_HUB_AND_ANALYTICS.md` |
