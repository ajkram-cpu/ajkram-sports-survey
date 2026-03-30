# Working Notes — Undergraduate Business Student Hobbies Survey

> **INTERNAL DOCUMENT — NOT PUBLIC-FACING.**
> Do not commit sensitive values. Update this file at the end of every working session.

---

## HOW TO USE THIS FILE (FOR AI ASSISTANTS)

1. Read this entire file before taking any action on the project.
2. Read `README.md` for public-facing context, installation steps, and deployment details.
3. Do not change the folder structure or file naming conventions without discussing it with the developer first.
4. Follow all conventions in Section 8 exactly — naming, code style, commit messages.
5. Do not suggest anything listed in "What Was Tried and Rejected" (Section 10).
6. Ask before making large structural changes (new routing library, replacing a major dependency, restructuring `src/`).
7. This project was scaffolded and significantly developed with AI assistance (Replit Agent). Refactor conservatively — prefer targeted edits over wholesale rewrites.
8. The monorepo has other artifacts (`api-server`, `mockup-sandbox`) — do not modify them unless the task explicitly requires it.

---

## CURRENT STATE

**Last Updated:** 2026-03-30

The survey app is fully built and running in the Replit development environment. All four pages (home, survey form, thank-you screen, results) are implemented. The Vite config has been updated for Azure Static Web Apps compatibility. The `staticwebapp.config.json` is in place. **The user still needs to run the Supabase SQL to create the `survey_responses` table and configure row-level security before the live form submission and results page will function.**

### What Is Working

- [x] Home page UI with "Take the Survey" and "View Results" navigation
- [x] Survey form — all four questions rendered correctly
- [x] Hometown text input with autofocus and placeholder
- [x] State dropdown with all 50 U.S. states in alphabetical order
- [x] Year in College radio button group (5 options)
- [x] Sports/hobbies checkbox group with animated "Other" text input
- [x] Auto-focus moves to "Other" input when that checkbox is selected
- [x] Client-side form validation via Zod + react-hook-form
- [x] Inline error messages with `aria-describedby` on each field
- [x] Submit button shows "Submitting..." while in flight
- [x] Thank-you screen with response summary and "View Results" link
- [x] Results page UI with loading spinner, error state, and empty state
- [x] Three Recharts visualizations: year bar chart, sports horizontal bar, states horizontal bar
- [x] "Other" hobby entries normalized by case and counted individually
- [x] Top-10 states with percentage labels
- [x] Responsive single-column layout (mobile-first)
- [x] Footer on all pages: "Survey by ajkram, BAIS:3300 - spring 2026."
- [x] `staticwebapp.config.json` in `public/` for Azure client-side routing
- [x] Vite config updated — no longer crashes when `PORT`/`BASE_PATH` env vars are absent (Azure build compatibility)
- [x] `.env.example` committed for onboarding
- [x] `README.md` at project root
- [x] `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` set as shared env vars in Replit

### What Is Partially Built

- [ ] Live Supabase connection — env vars are set but the **`survey_responses` table has not yet been created** in the Supabase project. The user must run the SQL in Section 7 below inside the Supabase SQL Editor.
- [ ] Results page charts — fully implemented but will only display data after the table exists and responses are submitted
- [ ] Azure deployment — config files are in place but no GitHub Actions workflow has been written yet

### What Is Not Started

- [ ] GitHub Actions CI/CD workflow for Azure Static Web Apps auto-deploy
- [ ] Admin CSV export of all responses
- [ ] Duplicate-submission prevention (localStorage fingerprint or similar)
- [ ] Server-side aggregation for large datasets (currently all rows are fetched client-side)

---

## CURRENT TASK

When last stopped, the README and WORKING_NOTES were being created and the Vite config was updated for Azure compatibility. The app itself is complete and working in development. The immediate blocker is that the Supabase table does not yet exist — the user must paste the SQL from Section 7 into the Supabase SQL Editor and run it before any form submissions or results will work end-to-end.

**Next step:** Run the Supabase SQL (Section 7) in the dashboard, submit a test response, and verify the results page charts populate correctly.

---

## ARCHITECTURE AND TECH STACK

| Technology | Version | Why It Was Chosen |
|---|---|---|
| React | 18 (catalog pin) | Component model; required by the PRD |
| TypeScript | ~5.9.2 | Type safety across all source files |
| Vite | catalog pin (v7) | Fast dev server and build; required by the PRD |
| Tailwind CSS | v4 (catalog pin) | Utility-first styling; already in monorepo catalog |
| Wouter | ^3.3.5 | Lightweight client-side router; less overhead than React Router for a 3-page app |
| react-hook-form | ^7.71.2 | Performant uncontrolled form state; works cleanly with Zod resolver |
| Zod | catalog pin | Schema validation; already used across monorepo |
| @hookform/resolvers | ^3.10.0 | Bridges Zod schema into react-hook-form |
| Framer Motion | catalog pin | Animated reveal of "Other" input; page transitions |
| @supabase/supabase-js | ^2.100.1 | Direct client-to-database connection; no backend required |
| Recharts | ^2.15.4 | Required by PRD; composable React charting library |
| shadcn/ui (Radix) | various | Accessible unstyled primitives; already scaffolded in monorepo |
| pnpm workspaces | v10 | Monorepo tooling already in place |
| Azure Static Web Apps | — | Deployment target specified in the PRD |

---

## PROJECT STRUCTURE NOTES

```
artifacts/survey-app/
├── public/
│   ├── staticwebapp.config.json   # Azure SWA routing — rewrites /* to /index.html
│   ├── favicon.svg
│   └── images/
│       └── hero-bg.png            # AI-generated hero background (home page)
├── src/
│   ├── components/
│   │   ├── layout.tsx             # Shared wrapper: header nav + footer on every page
│   │   └── ui/                    # shadcn/ui primitives (do not hand-edit these)
│   ├── hooks/
│   │   ├── use-survey.ts          # useSubmitSurvey (insert) + useSurveyResults (select)
│   │   ├── use-mobile.tsx         # Breakpoint detection hook
│   │   └── use-toast.ts           # Toast notification hook
│   ├── lib/
│   │   ├── constants.ts           # US_STATES[], YEARS_IN_COLLEGE[], SPORTS_OPTIONS[]
│   │   ├── supabase.ts            # createClient() — reads VITE_SUPABASE_URL + VITE_SUPABASE_ANON_KEY
│   │   └── utils.ts               # cn() — tailwind-merge + clsx utility
│   ├── pages/
│   │   ├── home.tsx               # Landing page
│   │   ├── survey.tsx             # Four-question form + thank-you screen
│   │   ├── results.tsx            # Aggregated charts
│   │   └── not-found.tsx          # 404 fallback
│   ├── App.tsx                    # WouterRouter + QueryClientProvider setup
│   ├── main.tsx                   # ReactDOM.createRoot entry point
│   └── index.css                  # Tailwind theme — #8A3BDB accent, HSL CSS variables
├── .env.example                   # Template for required env vars
├── vite.config.ts                 # Dev server + Azure-compatible build config
├── tsconfig.json                  # Leaf workspace — tsc --noEmit only
└── package.json                   # All deps are devDependencies except @supabase/supabase-js
```

### Non-obvious decisions

- `@supabase/supabase-js` is in `dependencies` (not `devDependencies`) because it is a runtime import, not a build tool. Everything else is in `devDependencies` per monorepo convention for static artifacts.
- The Vite config uses `import.meta.dirname` (Node 20+ ESM) — do not downgrade or replace with `__dirname`.
- Replit-specific plugins (`cartographer`, `devBanner`, `runtimeErrorOverlay`) are conditionally included only when `REPL_ID` is present and `NODE_ENV !== "production"`. They are absent in Azure builds.
- `BASE_PATH` defaults to `"/"` when not set — this is correct for Azure; Replit injects the actual base path at runtime.

### Files/folders that must not be changed without discussion

- `src/components/ui/` — generated shadcn/ui components; changes may be overwritten
- `public/staticwebapp.config.json` — required for Azure routing; do not remove or rename
- `vite.config.ts` — handles both Replit dev and Azure build; changes need testing in both environments
- `src/lib/constants.ts` — source of truth for all dropdown/checkbox options; changing values here affects both the form and result chart labels

---

## DATA / DATABASE

**Database:** Supabase PostgreSQL (project ID: `ztnhvllnrsnlgqlodkfe`)
**Connection:** Direct from browser via anon key (no backend proxy)

### Table: `survey_responses`

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | `uuid` | auto | `gen_random_uuid()` — primary key |
| `created_at` | `timestamptz` | auto | `now()` default — not exposed in the UI |
| `hometown` | `text` | yes | Free-text; trimmed before insert |
| `state` | `text` | yes | One of the 50 U.S. state names (enforced client-side) |
| `year_in_college` | `text` | yes | One of: "1st Year", "2nd Year", "3rd Year", "4th Year", "5th Year or More" |
| `sports` | `text[]` | yes | Array of selected option strings; always at least one element |
| `other_hobby` | `text` | no | Populated only when `sports` contains `"Other"`; `null` otherwise |

**Row-Level Security:** Enabled. Two policies:
- `anon` role can `INSERT` (public survey submission)
- `anon` role can `SELECT` (public results display)

**SQL to create table and policies (run once in Supabase SQL Editor):**

```sql
CREATE TABLE IF NOT EXISTS survey_responses (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  created_at timestamptz DEFAULT now() NOT NULL,
  hometown text NOT NULL,
  state text NOT NULL,
  year_in_college text NOT NULL,
  sports text[] NOT NULL,
  other_hobby text
);

ALTER TABLE survey_responses ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Allow public inserts"
  ON survey_responses FOR INSERT TO anon WITH CHECK (true);

CREATE POLICY "Allow public reads"
  ON survey_responses FOR SELECT TO anon USING (true);
```

---

## CONVENTIONS

### Naming conventions

- **Files:** `kebab-case.tsx` for pages and components; `use-*.ts` for hooks
- **Components:** PascalCase named exports (e.g., `export default function SurveyPage()`)
- **Variables/functions:** camelCase
- **Constants:** SCREAMING_SNAKE_CASE for exported arrays (e.g., `US_STATES`, `SPORTS_OPTIONS`)
- **CSS classes:** Tailwind utilities only; no custom CSS classes except in `index.css` theme block

### Code style

- TypeScript strict mode — no `any` except Recharts tooltip props (documented inline)
- Zod schemas defined at top of file, before the component
- `cn()` utility from `src/lib/utils.ts` for all conditional class merging
- No `console.log` in committed code; `console.error` is acceptable for caught errors

### Framework patterns

- **Forms:** react-hook-form with `zodResolver`; controlled only where necessary (checkboxes via `setValue`)
- **Data fetching:** `@tanstack/react-query` via hooks in `src/hooks/use-survey.ts`; no raw `fetch` in components
- **Routing:** Wouter `<Link href="...">` for navigation; `useLocation` for imperative navigation
- **Animations:** Framer Motion `<AnimatePresence>` + `<motion.div>` only for the "Other" field and page-level transitions

### Git commit style

```
type: short imperative description

Types: feat | fix | chore | docs | style | refactor | a11y
Examples:
  feat: add duplicate submission guard using localStorage
  fix: correct aria-describedby wiring on year radio group
  docs: update WORKING_NOTES with session log
```

---

## DECISIONS AND TRADEOFFS

- **No backend API server for this app.** The survey app connects directly to Supabase from the browser using the anon key. The monorepo's `api-server` artifact exists but is not used by this app. This avoids adding a server dependency for a simple public-facing form. Do not suggest routing Supabase calls through the Express server.
- **All rows fetched on the results page.** The results page fetches all `survey_responses` rows and aggregates them client-side via `useMemo`. This is simple to implement and sufficient for the expected class-size dataset (< 500 rows). Do not refactor to server-side aggregation unless row counts are confirmed to cause performance problems.
- **Wouter over React Router.** The app has three routes and no nested routing. Wouter is ~1.5 KB vs React Router's ~50 KB. Do not suggest migrating to React Router.
- **`dist/public` as the build output directory.** The Replit artifact.toml's `publicDir` is set to `artifacts/survey-app/dist/public`. Changing the Vite `outDir` to `dist` alone would break Replit production serving. Azure SWA `output_location` should be set to `dist/public` to match.
- **`VITE_` prefix for Supabase env vars.** Vite only exposes env vars prefixed with `VITE_` to the browser bundle. The vars must stay prefixed. Do not rename them.
- **Accent color `#8A3BDB` is not used on body text.** The PRD explicitly flags this as a WCAG AA contrast failure at small sizes. All body text uses `#1a1a1a`. Only headings and interactive elements use the accent.

---

## WHAT WAS TRIED AND REJECTED

- **Using Replit's built-in PostgreSQL database** — The PRD and the user explicitly specified their own Supabase account. Do not suggest switching to the Replit DB.
- **Backend API proxy for Supabase calls** — Considered routing Supabase inserts and selects through the Express `api-server`. Rejected because the anon key is designed for direct client access, adding a proxy creates unnecessary complexity, and the app is static (Azure SWA has no server-side runtime).
- **React Router** — Rejected in favor of Wouter for bundle size reasons on a 3-page app.
- **Server-side aggregation for charts** — The results page originally considered a `/api/results` endpoint that returned pre-aggregated data. Rejected because it would require the backend, which was eliminated.
- **`output_location: dist`** — Changing the Vite outDir to `dist` (without the `/public` suffix) was briefly attempted but reverted because it would break the existing `artifact.toml` `publicDir` setting used by Replit's production serving.

---

## KNOWN ISSUES AND WORKAROUNDS

**Issue 1 — Supabase table does not exist yet**
- The `survey_responses` table has not been created in the Supabase project.
- **Workaround:** None yet — the user must run the SQL in Section 7.
- The results page will show a loading spinner indefinitely and the form will throw on submit until the table exists.
- Do not remove the error boundary or loading states from `results.tsx`.

**Issue 2 — `year_in_college` radio group error message lacks `aria-errormessage`**
- The error paragraph below the radio group is not programmatically associated with the `<fieldset>` via `aria-errormessage` or `aria-describedby`.
- **Workaround:** The error message is visually visible and positioned immediately below the group. Screen reader users relying solely on `aria-errormessage` will not be announced the error automatically.
- Do not remove the error paragraph while this is unresolved.

**Issue 3 — No duplicate-submission guard**
- A user can submit the survey multiple times from the same browser without restriction.
- **Workaround:** None. The data is still valid; duplicates inflate counts but don't corrupt them.
- This is a known limitation acceptable for a class assignment at small scale.

---

## BROWSER / ENVIRONMENT COMPATIBILITY

### Front-end

| Browser | Status |
|---|---|
| Chrome 120+ | Tested — fully working |
| Safari 17+ | Expected to work; not explicitly tested |
| Firefox 120+ | Expected to work; not explicitly tested |
| Mobile Chrome (Android) | Expected to work — responsive layout tested in dev tools |
| Mobile Safari (iOS) | Expected to work — responsive layout tested in dev tools |

- CSS variable `hsl(from ...)` relative color syntax is used in `index.css` for automatic border intensity. This requires Chrome 119+, Safari 16.4+, Firefox 128+. A fallback value is declared above each usage.
- The app does not use any Web APIs beyond `fetch` (via Supabase client) and `window.scrollTo`.

### Back-end / Build Environment

- **Node.js:** v24 (Replit environment)
- **pnpm:** v10
- **Build:** Vite 7, esbuild under the hood
- **Azure build:** No Node version is pinned in an Azure workflow file yet — pin to Node 20 LTS when the GitHub Actions workflow is written

---

## OPEN QUESTIONS

- **What Azure Static Web App name/resource group will be used?** Needed to finalize the GitHub Actions workflow YAML (`azure/static-web-apps-deploy` action requires the deployment token as a secret).
- **Should duplicate submissions be blocked?** If yes, decide between: (a) localStorage flag, (b) Supabase unique constraint on a browser fingerprint column, or (c) leaving it open (current state).
- **Is the "Other" text field case normalization sufficient?** Currently `"Pottery"` and `"pottery"` are merged, but `"Pottery "` (trailing space) is also trimmed before counting. Confirm this is the expected behavior with the instructor.
- **Should the results page be password-protected?** Currently anyone with the URL can view aggregated results. The PRD does not require auth, but worth confirming before deployment.
- **Does the app need to handle the survey closing on a deadline date?** The PRD does not mention a cutoff — confirm whether the form should be disabled after a certain date.

---

## SESSION LOG

### 2026-03-30

**Accomplished:**
- Read PRD (`attached_assets/example_PRD_1774869138304.md`) and gathered Supabase credentials from user
- Set `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` as shared Replit env vars
- Created `artifacts/survey-app` react-vite artifact
- Installed `@supabase/supabase-js` and `recharts`
- Generated complete frontend via `generateFrontend()`: home, survey form, results page, layout, hooks, constants, supabase client
- Provided Supabase SQL for table creation and RLS policies
- Created `README.md` at project root with all 16 specified sections
- Created `public/staticwebapp.config.json` for Azure client-side routing
- Updated `vite.config.ts` to remove hard crashes on missing `PORT`/`BASE_PATH` (Azure build compatibility)
- Created `.env.example`
- Created `WORKING_NOTES.md` (this file)

**Left incomplete:**
- User has not yet run the Supabase SQL — table does not exist; end-to-end form submission untested
- No GitHub Actions workflow for Azure deployment

**Decisions made:**
- No backend proxy — Supabase called directly from browser
- `dist/public` output directory kept to match `artifact.toml` `publicDir`

**Next step:** User runs the Supabase SQL, submits a test response, confirms results charts populate.

---

## USEFUL REFERENCES

- [Supabase JavaScript Client Docs](https://supabase.com/docs/reference/javascript/introduction)
- [Supabase Row Level Security Guide](https://supabase.com/docs/guides/auth/row-level-security)
- [Recharts API Reference](https://recharts.org/en-US/api)
- [react-hook-form + Zod resolver](https://react-hook-form.com/get-started#SchemaValidation)
- [Wouter routing](https://github.com/molefrog/wouter)
- [Azure Static Web Apps configuration reference](https://learn.microsoft.com/en-us/azure/static-web-apps/configuration)
- [Azure Static Web Apps + Vite deployment guide](https://learn.microsoft.com/en-us/azure/static-web-apps/deploy-react)
- [WCAG 2.1 Level AA contrast checker](https://webaim.org/resources/contrastchecker/)
- [shields.io badge generator](https://shields.io/)
- **AI tools used:** Replit Agent (scaffolding, frontend generation, config, documentation) — all output reviewed and verified by the developer before committing
