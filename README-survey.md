# Undergraduate Business Student Hobbies Survey

## Description

A short, mobile-friendly web survey that collects hometown, state, college year, and favorite sports from undergraduate business students. Responses are stored anonymously in a Supabase PostgreSQL database and displayed as interactive, aggregated charts on a live results page. The app was built for BAIS:3300 — spring 2026 to gather leisure hobby data from the Tippie College of Business student body.

## Badges

![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=black)
![TailwindCSS](https://img.shields.io/badge/TailwindCSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)
![Azure](https://img.shields.io/badge/Azure_Static_Web_Apps-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![MIT License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

## Features

- **Four-question survey** covering hometown, U.S. state, year in college, and favorite sports to watch
- **Dynamic "Other" hobby field** — appears automatically when checked, focus moves to it instantly, and requires a written description before submission
- **Full client-side validation** with inline error messages linked to each field via `aria-describedby`
- **Live results dashboard** with three Recharts visualizations: year-in-college bar chart, sports popularity chart, and top-10 states chart
- **Custom hobby aggregation** — "Other" entries are normalized by case and counted as individual categories alongside standard sports options
- **Thank-you screen** after submission showing a response summary and a direct link to the results page
- **Fully responsive** single-column layout that works on phones, tablets, and desktops
- **WCAG 2.1 Level AA accessible** — all inputs have associated labels, errors are programmatically announced, and keyboard navigation is fully supported

## Tech Stack

| Technology | Purpose |
|---|---|
| React 18 | UI component framework |
| TypeScript | Static typing across all source files |
| Vite | Build tool and local development server |
| Tailwind CSS v4 | Utility-first styling |
| Wouter | Lightweight client-side routing (`/`, `/survey`, `/results`) |
| react-hook-form | Form state management and submission handling |
| Zod | Survey form schema validation |
| Framer Motion | Page transitions and animated "Other" field reveal |
| Supabase JS | Direct client-to-database connection for insert and select |
| Recharts | Bar charts for year, sports, and state data |
| shadcn/ui (Radix) | Accessible UI primitives (select, radio group, checkbox) |
| Azure Static Web Apps | Production hosting with client-side routing support |

## Getting Started

### Prerequisites

- [Node.js v18+](https://nodejs.org/)
- [pnpm v9+](https://pnpm.io/installation)
- A [Supabase](https://supabase.com/) project (free tier is sufficient)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/ajkram/bais3300-survey.git
   cd bais3300-survey
   ```

2. Install all workspace dependencies:
   ```bash
   pnpm install
   ```

3. Create a `.env` file inside `artifacts/survey-app/`:
   ```bash
   cp artifacts/survey-app/.env.example artifacts/survey-app/.env
   ```

4. Fill in your Supabase credentials in `.env`:
   ```
   VITE_SUPABASE_URL=https://your-project-id.supabase.co
   VITE_SUPABASE_ANON_KEY=eyJ...your-anon-key...
   ```

5. Run the SQL below in your **Supabase SQL Editor** to create the table and configure row-level security:
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

6. Start the development server:
   ```bash
   pnpm --filter @workspace/survey-app run dev
   ```

## Usage

| Route | Description |
|---|---|
| `/` | Home page — welcome message, links to survey and results |
| `/survey` | Four-question form; submits to Supabase on completion |
| `/results` | Aggregated, anonymized charts pulled live from Supabase |

**Environment variables** (set in `.env` for local dev, or as Azure Application Settings for production):

| Variable | Description |
|---|---|
| `VITE_SUPABASE_URL` | Your Supabase project URL |
| `VITE_SUPABASE_ANON_KEY` | Your Supabase anon/public API key |

To build for production:
```bash
pnpm --filter @workspace/survey-app run build
```
Output is written to `artifacts/survey-app/dist/`.

## Project Structure

```
bais3300-survey/
├── artifacts/
│   └── survey-app/                  # The deployable React + Vite app
│       ├── public/
│       │   ├── staticwebapp.config.json  # Azure SWA client-side routing config
│       │   └── images/
│       │       └── hero-bg.png      # AI-generated hero background image
│       ├── src/
│       │   ├── components/
│       │   │   ├── layout.tsx       # Shared page wrapper with header and footer
│       │   │   └── ui/              # shadcn/ui component library
│       │   ├── hooks/
│       │   │   └── use-survey.ts    # React Query hooks for Supabase insert and select
│       │   ├── lib/
│       │   │   ├── constants.ts     # US states, year options, sports options arrays
│       │   │   ├── supabase.ts      # Supabase client initialization
│       │   │   └── utils.ts         # Tailwind class merge utility
│       │   ├── pages/
│       │   │   ├── home.tsx         # Landing page with welcome text and nav buttons
│       │   │   ├── survey.tsx       # Four-question form with validation and submission
│       │   │   ├── results.tsx      # Aggregated charts (year, sports, states)
│       │   │   └── not-found.tsx    # 404 fallback page
│       │   ├── App.tsx              # Router setup with wouter + React Query provider
│       │   ├── main.tsx             # React DOM entry point
│       │   └── index.css            # Tailwind theme with #8A3BDB accent variables
│       ├── vite.config.ts           # Vite config (dev server + Azure-compatible build)
│       ├── tsconfig.json            # TypeScript project config
│       └── package.json             # Package dependencies and npm scripts
├── lib/                             # Shared workspace libraries (API spec, DB, codegen)
├── pnpm-workspace.yaml              # pnpm monorepo workspace definition
├── package.json                     # Root scripts and shared dev tooling
└── README.md                        # This file
```

## Changelog

### v1.0.0 — 2026-03-30

- Initial release
- Home page with survey and results navigation
- Four-question survey form: hometown, state, year in college, favorite sports
- Dynamic "Other" hobby text input with auto-focus
- Client-side validation with inline, accessible error messages
- Supabase PostgreSQL integration (insert + select via anon key)
- Results dashboard: total responses, year bar chart, sports horizontal bar chart, top-10 states horizontal bar chart
- Thank-you screen with response summary
- Footer: "Survey by ajkram, BAIS:3300 - spring 2026"
- Azure Static Web App deployment configuration (`staticwebapp.config.json`)
- WCAG 2.1 Level AA accessibility
- Responsive mobile-first layout

## Known Issues / To-Do

- [ ] The `year_in_college` radio buttons do not have `aria-describedby` wired to the group error message — needs a wrapping `role="group"` with `aria-errormessage`
- [ ] Results page fetches all rows on every visit; large datasets (1,000+ rows) should use server-side aggregation instead of client-side `useMemo`
- [ ] No duplicate-submission guard — the same user can submit the survey multiple times without restriction
- [ ] The `other_hobby` field placeholder text does not update when the user has already typed a value and re-checks "Other"
- [ ] No loading skeleton on the results page; the spinner appears but the chart containers shift layout when data arrives

## Roadmap

- Add a GitHub Actions CI/CD workflow that builds and deploys to Azure Static Web Apps automatically on push to `main`
- Add an admin-only export route that lets the instructor download all responses as a CSV
- Show a "Responses near you" map visualization using respondents' states and a choropleth layer
- Add a duplicate-submission guard using a browser fingerprint stored in `localStorage`
- Internationalize the sports options list so the survey can be reused in future semesters with different hobby categories

## Contributing

This project is a course assignment. Pull requests are welcome for bug fixes and accessibility improvements. Please open an issue first to discuss any larger changes.

1. Fork the repository
2. Create a feature branch: `git checkout -b fix/your-bug-name`
3. Commit your changes with a clear message: `git commit -m "fix: describe what changed"`
4. Push to your fork: `git push origin fix/your-bug-name`
5. Open a Pull Request against the `main` branch and describe the problem you solved

## License

This project is licensed under the [MIT License](LICENSE).

## Author

**ajkram**
Tippie College of Business, University of Iowa
BAIS:3300 — Business Applications of Artificial Intelligence, Spring 2026

## Contact

GitHub: [https://github.com/ajkram](https://github.com/ajkram)

## Acknowledgements

- [Supabase Documentation](https://supabase.com/docs) — database setup, RLS policies, and JavaScript client
- [Recharts](https://recharts.org/) — composable charting library for React
- [shadcn/ui](https://ui.shadcn.com/) — accessible, unstyled Radix UI components
- [react-hook-form](https://react-hook-form.com/) — performant form state management
- [Zod](https://zod.dev/) — TypeScript-first schema validation
- [Wouter](https://github.com/molefrog/wouter) — lightweight client-side router
- [Framer Motion](https://www.framer.com/motion/) — animation library for the "Other" field reveal and page transitions
- [Azure Static Web Apps documentation](https://learn.microsoft.com/en-us/azure/static-web-apps/) — deployment and routing configuration
- [Replit Agent](https://replit.com/) — AI-assisted development environment used to scaffold and build this application
- [shields.io](https://shields.io/) — badge generation for this README

