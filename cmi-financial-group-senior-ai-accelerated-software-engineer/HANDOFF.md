# Handoff: CMI Application Tailoring

**For the next Claude Code agent picking this up at work.**

## Immediate situation

The user is mid-application to **CMI Financial Group — Senior AI Accelerated Software Engineer** ($160K–$180K, remote, permanent). We were about to draft tailored Resume.md and cover_letter.md when the user flagged a factual correction:

> **The Room Booking System is NOT in production.** It is in QA, pending political / bureaucratic approval to go live. The existing master cover letter line "Both are live, serving real users" overstates this. Do NOT repeat that claim.

The user opened this session at work to share their actual work history so tailoring can be grounded in reality rather than the existing (resume-optimized) Resume.md.

## What to do first

1. Greet the user and confirm you've read this file.
2. Ask how they want to share work history:
   - Paste into chat
   - Drop a file anywhere in the repo and tell you the path
   - Scrape their public LinkedIn (`https://www.linkedin.com/in/william-tucker-06203044/`) via `firecrawl-scrape` skill
3. Read everything carefully. Cross-reference against `Resume.md` in this folder. Note discrepancies, under-claims, and gaps vs the job description.
4. Propose tailoring edits — don't just rewrite. Flag anything that changes the claim. Get approval per section.
5. When tailoring is done, help regenerate PDFs if the user asks (they have a `md_to_pdf.py` script somewhere — ask).
6. Update `STATUS.md` with real dates as the application moves (drafting → applied → interviewing → won/closed).

## Current repo state

- **Working directory:** `D:/code/cv` (or wherever the user cloned it at work — verify with `pwd` and `git remote -v`)
- **Current branch:** `apply/cmi-financial-group-senior-ai-accelerated-software-engineer`
- **Files in this folder (this branch's application scaffold):**
  - `Resume.md` — untailored copy of master
  - `Resume.html`, `Resume.pdf` — untailored copies of master (will need regen after tailoring)
  - `cover_letter.md` — untailored copy of master (the "live, serving real users" line needs correction)
  - `cover_letter.html` — untailored copy of master
  - `references.md`, `references.html` — generic references
  - `job_description.md` — full CMI job posting, scraped from Indeed, with fit assessment at bottom
  - `STATUS.md` — currently `drafting`, last-activity 2026-04-16
  - `HANDOFF.md` — this file

## Job description quick reference

CMI wants a senior engineer (8+ yrs) who "built systems the traditional way, and now deliver faster and more effectively by working with AI — without giving up judgment, accountability, or quality."

**Strong matches for William:**
- 12+ years senior engineering (exceeds 8+ required)
- VIU Facilities Information System modernization: Classic ASP → .NET 8 Blazor in 2 days using AI agents. Zero breaking changes. Adopted as institutional reference. **This is genuinely in production.**
- Room Booking rebuild: 705K-line Oracle PL/SQL → .NET 8 Blazor. **In QA, pending political go-live approval — NOT in production. Frame honestly.**
- WTS Solutions consulting: AI-champion role, explicit knowledge transfer, production guardrails
- API/integration work at VIU (9 REST endpoints, cloud ERP via REST/SOAP)
- Oracle + SQL Server + PostgreSQL depth
- CI/CD across 30+ repos with health gates and rollback

**Honest gaps (do NOT fabricate):**
- No CakePHP production experience (PHP on list from 2012 co-op, nothing recent)
- No Salesforce / Apex / Flows
- No AWS (cloud experience is Vercel + Supabase)
- No Laravel / Symfony either
- MySQL is listed but primary DB work has been Oracle / SQL Server / Postgres

Acknowledge these upfront in the cover letter. Don't pretend they're transferable skills — be straightforward and anchor on the AI-accelerated-delivery strengths.

## Repo design (you MUST follow)

Full spec: `docs/superpowers/specs/2026-04-16-cv-application-tracking-design.md`
Cleanup plan (completed): `docs/superpowers/plans/2026-04-16-cv-repo-cleanup.md`

### Invariants

- **Master branch is generic only.** Never commit application folders to master.
- **Branch naming:** `<status>/<company-kebab>-<role-kebab>[-<jobid>]`
  - `apply/` = active, submitted, not yet heard back
  - `interview/` = in interview process
  - `won/` = offer received
  - `closed/` = rejected, expired, withdrawn
- **Folder name equals branch suffix.** For this branch, that's `cmi-financial-group-senior-ai-accelerated-software-engineer/`.
- **Branches never merge back to master.** They live forever on their own branch (or get deleted ~90 days after close).
- **Minimum per folder:** `Resume.md`, `Resume.pdf`, `cover_letter.md`, `cover_letter.pdf`, `job_description.md`, `STATUS.md`. Extras are fine.
- **`prospects/` is gitignored.** Unstarted leads go there. Graduates into a branch-suffix folder when user decides to apply.

## Flow: apply → interview → outcome

When the user has submitted the CMI application:

```bash
# Already on apply/cmi-... branch — update STATUS.md applied date + timeline, then:
git add cmi-financial-group-senior-ai-accelerated-software-engineer/STATUS.md
git commit -m "STATUS: applied 2026-MM-DD"
git push -u origin apply/cmi-financial-group-senior-ai-accelerated-software-engineer  # optional backup
```

When status changes:

```bash
# Got an interview:
git branch -m apply/cmi-... interview/cmi-...
# Update STATUS.md, commit
git push origin interview/cmi-... :apply/cmi-...   # push new, delete old remote

# Offer:     git branch -m interview/cmi-... won/cmi-...
# Rejected:  git branch -m apply/cmi-... closed/cmi-...   (or from interview/)
```

`STATUS.md` format (see other apply/* branches for real templates). Fill in real dates — the `<TODO>` tokens in other branches are placeholders the user hasn't filled yet.

## Guardrails for you

- **Don't overclaim.** If the user's work history doesn't support a resume claim, flag it. This job specifically emphasizes accountability.
- **Don't hallucinate tech.** If the history doesn't mention AWS / CakePHP / Salesforce, don't insinuate exposure.
- **Run verification before asserting.** Use the `superpowers:verification-before-completion` skill when closing work.
- **Ask before editing.** Propose tailoring changes section by section. Get approval. Don't rewrite the whole resume in one shot and hope.
- **Room booking = QA, not production.** Burn this into your context.
- **Don't commit to master.** Verify `git branch --show-current` returns `apply/cmi-financial-group-senior-ai-accelerated-software-engineer` before any commit.
- **Watch out for line endings.** The repo on Windows uses CRLF; git warns about LF→CRLF. Cosmetic, not a bug.

## Memory system

The user has an auto-memory directory at `C:\Users\bill\.claude\projects\D--code-cv\memory\`. Two entries relevant to this work:
- `project_application_workflow.md` — branch workflow (now superseded by the spec in `docs/superpowers/specs/`)
- `feedback_stale_dates.md` — always audit all files for stale dates before delivering

Update memory if the user gives you new preferences or feedback during the session.

## Tech stack at CMI (for reference when tailoring)

- **Application:** CakePHP (PHP 8.x presumed)
- **AI tooling:** GitHub Copilot, Claude, Cursor (user actively uses Claude + Claude Code — big match)
- **Enterprise:** Salesforce (Apex, Flows)
- **Data:** MySQL
- **Infra:** AWS, Git, Linux

When the user shares work history, look for any ACTUAL exposure to any of these. Don't infer. If they had a MySQL project in 2015, it's worth mentioning (softly — "working familiarity"). Don't call one project "expertise."

## Summary: your first message back to the user

Something like:
> Read HANDOFF.md. I'm on branch `apply/cmi-financial-group-senior-ai-accelerated-software-engineer` and I see the scaffold is in place but the Resume.md / cover_letter.md are still master copies. Noted the room booking correction — I won't claim it's in production. How do you want to share your work history — paste, file path, or LinkedIn scrape?

Good luck.
