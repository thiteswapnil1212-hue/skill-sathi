# Skill Sathi

**Smarter access to government benefits.**

Skill Sathi (original project name: Scheme Sathi) is a citizen decision-support system. It helps a person understand which government schemes they may qualify for, which schemes can work together, which combination is most useful, what documents are missing, and what to do next.

It was built as a team hackathon project for PS16 — Autonomous Scheme-Bundle Optimizer for Citizens (AI/ML + Agentic AI + GovTech).

> **Important:** Skill Sathi is not a government authority and does not submit applications on behalf of citizens. Results say **“Potentially Eligible”**, not “Officially Eligible”. Eligibility is based on configured scheme rules and must be confirmed with the official authority before applying.

## The five questions it answers

1. Which schemes may I qualify for?
2. Which schemes can work together?
3. Which combination is most useful for me?
4. What documents am I missing?
5. What should I do next?

## How it works

```text
Citizen Profile
      ↓
Scheme Knowledge Base
      ↓
Eligibility Engine
      ↓
Conflict Detection
      ↓
Bundle Generation
      ↓
Bundle Optimization
      ↓
Document Check
      ↓
Application Plan
      ↓
Personalized Explanation
```

**Deterministic rules decide eligibility and compatibility. AI is only the explanation layer.** That is the core design rule: government eligibility facts are decided by deterministic code, not by the language model. Gemini is used for orchestration, personalization, and explanation of the deterministic result.

## What it does

- **Citizen profile form** — only the information required for the analysis is collected.
- **Curated scheme knowledge base** — 50 Maharashtra state + Central Government schemes applicable in Maharashtra, in `data/schemes.seed.json` (research-curated, 2026-09-12; official government portals prioritized). This is a curated hackathon dataset, not the complete Indian scheme ecosystem.
- **Deterministic eligibility evaluation** — results use only `potentially_eligible`, `not_eligible`, or `insufficient_data`, with reasons the user can read.
- **Conflict detection** — identifies schemes that cannot be combined.
- **Compatible bundle generation and optimization** — builds and ranks combinations of schemes that can work together.
- **Document readiness** — lists the documents a bundle needs and which ones are missing.
- **Application plan** — ordered next steps for each recommended bundle.
- **Scheme details** — a page per scheme at `/scheme/[schemeId]`, with source and trust information.
- **Gemini-generated explanation** — a personalized explanation of the result, grounded in the deterministic analysis.

## Pages

| Route | What it is |
| --- | --- |
| `/` | Home — the five-step journey and what the tool does |
| `/find-benefits` | Citizen profile form |
| `/analysis` | Analysis in progress / result summary |
| `/results` | Eligibility, conflicts, and recommended bundle |
| `/results/bundle` | Recommended bundle detail |
| `/results/documents` | Document readiness and missing documents |
| `/results/application-plan` | Ordered application steps |
| `/scheme/[schemeId]` | Single scheme detail |

## API

- `GET /api/schemes` — returns the curated scheme catalog. Returns a `scheme_catalog_unavailable` error if catalog validation fails.
- `POST /api/analyze` — accepts `{ citizenProfile, availableDocuments }` and returns eligibility results, conflicts, the recommended bundle (or `null`), missing documents, and the application plan.

## Tech stack

Next.js 16, React 19, TypeScript, Tailwind CSS, shadcn/ui, Lucide icons, Gemini API (`@google/genai`). Business logic is plain TypeScript in `src/backend/`; the knowledge base is JSON. See `package.json` for the full list.

## Start locally

Prerequisites: Node.js 20+ and npm.

```bash
npm install
cp .env.example .env.local
npm run dev
```

Then open `http://localhost:3000`.

On Windows PowerShell, use `Copy-Item .env.example .env.local` instead of `cp`.

### Environment variables

Copy `.env.example` to `.env.local` and fill in real values. Never commit `.env.local`.

- `GEMINI_API_KEY` — server-side Gemini key, used for the explanation layer. Required for Gemini-generated explanations.
- `GEMINI_MODEL` — Gemini model name (example in `.env.example`: `gemini-2.5-flash`).

Eligibility, conflicts, and bundle logic do not depend on Gemini; they run on the deterministic engines in `src/backend/engines/`.

## Quality checks

```bash
npm run lint
npm run build
```

## Project structure

```text
src/app/              Pages and API routes (profile, analysis, results, scheme detail)
src/backend/          Deterministic engines, data layer, services, and agent code
src/backend/engines/  Eligibility, conflict, bundle, optimizer, document, and plan engines
src/backend/data/     Scheme repository, mapper, and validator
src/components/       UI components
data/                 Curated scheme dataset (schemes.seed.json)
docs/                 PRD, API contract, and technical requirements
```

See [docs/PRD.md](docs/PRD.md), [docs/API_CONTRACT.md](docs/API_CONTRACT.md), and [docs/TECHNICAL_REQUIREMENTS.md](docs/TECHNICAL_REQUIREMENTS.md) for the full product and technical detail. Note that `docs/API_CONTRACT.md` describes the analysis contract as future work in places; the `POST /api/analyze` route is implemented in `src/app/api/analyze/route.ts`.

## Honest limits

- The scheme dataset is curated and research-based. Detailed eligibility, income limits, dates, and document requirements change — always re-verify on the official portal or with the issuing authority before applying.
- Results are decision support, not an official eligibility decision and not an application submission.
- Only minimal profile data is collected for the analysis.

## Team and origin

This repository is an imported copy of the original team repository, [AmanShejul/scheme-sathi-](https://github.com/AmanShejul/scheme-sathi-) (the trailing hyphen is part of the original repository name). Commit history is preserved from the original. It is a team hackathon project; this copy is maintained under the Skill Sathi name.

## License

MIT — see [LICENSE](LICENSE).
