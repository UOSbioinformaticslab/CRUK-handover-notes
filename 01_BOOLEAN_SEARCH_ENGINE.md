# Feature Note 01: Advanced AST Boolean Search & Query Logic Engine

**Target Audience**: CRUK Technical Staff & Software Engineers  
**Module Location**: `cruk_datahub_landing_page/src/utils/` & `cruk_datahub_landing_page/src/components/FilterApp.jsx`  
**Status**: `[STATUS: PROTOTYPE COMPLETE - READY FOR BACKEND PORTING]`  
**Last Updated**: 2026-10-07  

---

## 1. Feature Architecture & Overview

The **Advanced AST Boolean Search Engine** enables researchers to build complex, PubMed-style structured Boolean queries across thousands of cancer research datasets. It allows researchers to combine filter criteria across multiple domain categories (Cancer Types, Data Types, Access Restrictions) using nested `AND` / `OR` logic and explicit parentheses grouping.

### Core Data Flow & Abstract Syntax Tree (AST) Architecture

```
┌─────────────────────────────────┐
│ User UI Selections (Checkbox)   │
│ selectedFilters Set('0_0_2_1')  │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ Token Generator (logic-utils.js)│
│ calculateLogicTokens()          │
└─────────────────────────────────┘
                 │  Outputs Array of JSON AST Tokens:
                 │  [{ type: 'bracket', value: '(' }, { type: 'filter', id: '0_0_2_1' }, ...]
                 ▼
┌─────────────────────────────────┐
│ Interactive Builder (FilterApp) │ ◄── [User toggles AND/OR, inserts Brackets]
│ Token UI with Prune/Toggle      │
└─────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│ Recursive Evaluator             │
│ (src/utils/filterLogic.js)      │
└─────────────────────────────────┘
                 │  Executes parseOr() -> parseAnd() -> parsePrimary()
                 │  Performs native JavaScript Set Union (OR) & Intersection (AND)
                 ▼
┌─────────────────────────────────┐
│ Dataset ID Results              │
│ Dispatched via Custom Event     │
└─────────────────────────────────┘
```

### Key Subsystems & Files

1. **`src/utils/logic-utils.js` ([logic-utils.js](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/utils/logic-utils.js))**:
   - `calculateLogicTokens(selectedFilters, filterDetailsMap)`: Automatically groups raw filter IDs by taxonomy category (Topography, Histology, Data Types, Access).
   - Generates an array of structured JSON tokens.
   - Automatically injects parent node dependencies to ensure complete category resolution.

2. **`src/components/FilterApp.jsx` ([FilterApp.jsx](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/components/FilterApp.jsx))**:
   - Renders the interactive **Advanced Logic Builder**.
   - Provides live controls for:
     - **AND/OR Toggling**: Click any operator token to swap between `AND` and `OR`.
     - **Bracket Insertion**: Select a range of terms/operators and click **Bracket** to safely wrap them in matching `(` and `)`.
     - **Term Pruning**: Individual `✕` buttons on each token.
     - **Reset to Auto Logic**: One-click restore to system-calculated AST.

3. **`src/utils/filterLogic.js` ([filterLogic.js](file:///Users/skw24/CRUK/website/CRUK_datahub_landing_page/src/utils/filterLogic.js))**:
   - `executeFilterLogic(tokens, datasetRecords)`: Evaluates the query without using `eval()`.
   - Built around a formal **Recursive Descent Parser**:
     - `parseOr()`: Evaluates `OR` expressions (computes `Set` union: $A \cup B$).
     - `parseAnd()`: Evaluates `AND` expressions (computes `Set` intersection: $A \cap B$).
     - `parsePrimary()`: Evaluates parenthesized expressions `(...)` or primitive filter IDs.
   - Completely safe against code injection vectors.

---

## 2. Prototype (`cruk_datahub_landing_page`) vs. Production (`ayo/gateway-web`) Comparison

| Technical Attribute | Prototype (`cruk_datahub_landing_page`) | Production (`ayo/gateway-web`) |
| :--- | :--- | :--- |
| **Search Mechanism** | Full AST token parsing + client-side `Set` arithmetic | Standard URL query params + REST API faceted filters |
| **Logic Operands** | Nested Boolean (`AND`, `OR`, brackets `()`) | Simple multi-select implicit `AND` |
| **Execution Environment** | Client browser runtime (evaluated in memory) | Next.js API / Backend Gateway API 2.0 SQL/Elasticsearch query |
| **Query Manipulation UI** | Interactive visual token builder & bracket inserter | Static filter checkboxes & text input field |
| **Security** | Safe recursive descent parser (0 `eval()`) | Standard parameterized REST backend requests |

---

## 3. SWOT Analysis

### Strengths
- **Empowering UX**: Researchers can construct targeted, multi-variable queries impossible with basic multi-select dropdowns.
- **Zero Security Risk**: Eliminates `eval()` vulnerability through formal AST token parsing.
- **High Performance**: Instant client-side set calculations without latency or server calls during exploration.

### Weaknesses
- **Memory Overhead**: Scalability limit when evaluating hundreds of thousands of dataset IDs directly in client browser memory.
- **State Divergence**: Custom token edits must be carefully managed to avoid getting out of sync with raw checkbox states.

### Opportunities
- **Backend Translation**: The JSON token array structure can be converted into SQL `WHERE` clauses or Elasticsearch boolean JSON queries on the backend server.
- **Next.js Server Actions**: Execute the AST parser inside Next.js Server Components / API routes to combine client-side flexibilty with server-side dataset scale.

### Threats
- **Syntax Errors**: Invalid bracket matching from manual token manipulation could confuse non-technical users if UI validation banners are ignored.

---

## 4. Migration & Implementation Roadmap for CRUK Engineering Team

To port this feature into `ayo/gateway-web`:

1. **Port AST Token Utilities**:
   - Copy `logic-utils.js` and `filterLogic.js` into `ayo/gateway-web/src/utils/filter/`.
   - Add TypeScript definitions (`Token`, `TokenType`, `FilterNode`) in `ayo/gateway-web/src/interfaces/filter.ts`.

2. **Component Integration in Next.js**:
   - Create `LogicBuilderWidget.tsx` inside `ayo/gateway-web/src/modules/FilterPagination/`.
   - Connect token state to URL search parameters using Next.js `useSearchParams()` and `useRouter()`.

3. **Backend SQL/Elasticsearch Translation**:
   - Expand `executeFilterLogic` to support an alternate compile target: `toSqlWhereClause(tokens)` or `toElasticsearchQuery(tokens)`.
   - Pass compiled query payload directly to the HDRUK Gateway API 2.0 `/datasets` query endpoint.

---

## 5. Maintenance & Revision Log

| Date | Author | Description of Changes | Affected Files |
| :--- | :--- | :--- | :--- |
| **2026-10-07** | Sussex Bioinformatics Lab | Documented AST Boolean logic engine architecture & security refactoring. | `01_BOOLEAN_SEARCH_ENGINE.md` |
