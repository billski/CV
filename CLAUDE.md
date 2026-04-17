# CLAUDE.md — instructions for this repo

This repo tracks William Tucker's CVs and job applications. Every branch except `master` corresponds to a single application.

## Before doing anything application-related

**Read `PROFILE.md` first.** It is the source of truth for William's background, verified project inventory, honest tech-stack assessment, and framing rules. Do not claim qualifications from resume copy or memory alone — those can drift. `PROFILE.md` is rebuilt from actual repos and is the reference.

If the user asks "am I qualified for X?" or "does my experience match this posting?", read `PROFILE.md` section 12 (role-fit heuristics) before answering. If the question needs evidence `PROFILE.md` doesn't cover, verify directly against the relevant repo — commands are in `PROFILE.md` section 13.

## Repo model

- **Master branch** = generic materials only: `PROFILE.md`, base `Resume.md` / `Resume.pdf`, base `cover_letter.md`, base `references.md`, this file, `README.md`, and design docs under `docs/`.
  - **Never commit a per-application folder to master.**
- **Application branches** = per-job, named `<status>/<company-kebab>-<role-kebab>[-<jobid>]`.
  - `apply/` = submitted, not yet heard back
  - `interview/` = in interview process
  - `won/` = offer received
  - `closed/` = rejected, expired, withdrawn
- **Folder name equals branch suffix** (e.g. branch `apply/acme-senior-swe` → folder `acme-senior-swe/`).
- **Branches never merge back to master.** They live on their own branch permanently (or get deleted ~90 days after close).
- **Minimum per application folder:** `Resume.md`, `Resume.pdf`, `cover_letter.md`, `cover_letter.pdf`, `job_description.md`, `STATUS.md`.
- **`prospects/` is gitignored.** Unstarted leads graduate into a branch when William decides to apply.

Full spec: `docs/superpowers/specs/2026-04-16-cv-application-tracking-design.md`.

## Framing rules (enforced — see `PROFILE.md` §10 for details)

1. **AI-accelerated, not AI-replaced.** William does planning, debugging, and judgement himself; AI is a force multiplier. Never describe work as "generated" or "automated."
2. **RoomBooking is in QA, not production.** Go-live blocked on organizational approval, not engineering. Boss will vouch. Never claim it's live.
3. **BIS was ~7 weeks, not 2 days.** Earlier resume drafts had this wrong. Use the honest 7-week figure with 66 commits across 14 branches — it's a stronger claim than "2 days" because it demonstrates planned, iterated engineering.
4. **Do not infer transferable skills from adjacent tech.** PostgreSQL ≠ MySQL. Vercel ≠ AWS. Next.js ≠ CakePHP. Acknowledge gaps upfront in cover letters — credibility matters.
5. **Audit all files for stale dates** before delivering tailored copy. Convert relative dates to absolute when writing.
6. **Ask before editing.** Propose tailoring changes section by section and get approval. Never rewrite the whole resume in one shot.

## Git safety

Before any commit, verify `git branch --show-current` returns the expected branch. Never commit per-application folders to `master`. Never push to remote unless William explicitly asks.

Line-ending warnings (LF → CRLF) on this Windows repo are cosmetic — ignore them.

## Memory

William's auto-memory lives at `C:\Users\tuckerb\.claude\projects\C--code-cv\memory\`. Existing entries:
- `user_background.md` — verified repo inventory (mirrored into `PROFILE.md`)
- `user_ai_framing.md` — the AI-accelerated framing rule
- `project_roombooking_status.md` — the QA-not-production framing

If the user gives new feedback, preferences, or context, update memory immediately. If memory and `PROFILE.md` disagree, **`PROFILE.md` wins** — it's the verified-from-code reference. Update memory to match.

## When starting a new application

1. Verify current branch. If on `master`, create a new `apply/<company-role>` branch from master.
2. Create the application folder matching the branch suffix. Copy `Resume.md`, `cover_letter.md`, `references.md` from master as starting points.
3. Drop the job description into `job_description.md` and start a `STATUS.md` with `drafting` + today's date.
4. Read `PROFILE.md` §12 to assess fit honestly before tailoring.
5. Tailor section by section with William's approval. No unilateral rewrites.
