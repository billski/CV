# CV Application Tracking System — Design

**Date:** 2026-04-16
**Status:** Approved, pending implementation
**Scope:** Reorganize `D:/code/cv` git repository to track job applications consistently.

## Problem

The repo currently mixes generic resume content with job-specific tailoring in ad-hoc ways:

- Four `apply/*` branches with inconsistent naming (job ID prefix vs suffix vs absent).
- Untracked job folders on master (`Helm/`, `HillsdaleInvestmentManagement/`, `P00181/`) with wildly different contents.
- One job folder (`RentalHunt/`) committed to master with just a single `Resume.html` file.
- No way to tell from the repo which applications are active, in interview, won, or closed.
- File naming issues: `job_description.txt.txt` double extension; folder names in PascalCase, kebab-case, and job-code-only formats on different folders.

## Goals

1. Keep master pristine — generic resume/cover letter/references only.
2. One predictable place per application, with a consistent file set.
3. At-a-glance application status via `git branch`.
4. Room for ad-hoc per-application files (take-homes, portfolio links, interview notes) without forcing a fixed set.
5. A holding area for prospects you haven't decided to apply to yet, without polluting git history.

## Design

### Repository structure

```
D:/code/cv
├── master branch (generic only)
│   ├── Resume.{md,html,pdf}
│   ├── cover_letter.{md,html,pdf}
│   ├── references.{md,html,pdf}
│   ├── README.md
│   ├── docs/
│   └── .gitignore       (includes /prospects/, /.firecrawl/, /.claude/)
├── prospects/            (gitignored — unstarted leads)
└── .git/branches:
    apply/<slug>       active, submitted, not yet heard back
    interview/<slug>   in interview process
    won/<slug>         offer received
    closed/<slug>      rejected, expired, or withdrawn
```

### Branch naming

Format: `<status>/<company-kebab>-<role-kebab>[-<jobid>]`

- `<status>` is one of `apply`, `interview`, `won`, `closed`.
- Company and role always kebab-case.
- Job ID suffix (`-P02688`, `-12345`) optional — include only if the employer uses one.
- Status prefix changes via `git branch -m` as the application progresses.

Examples:
- `apply/matter-inc-sr-software-developer`
- `apply/okanagan-college-ai-developer-P02688`
- `interview/cmi-financial-group-senior-ai-engineer`
- `closed/rentalhunt-software-developer`

### Per-application folder

Folder name = the branch suffix (everything after the first `/`). This guarantees folder-to-branch traceability and supports multiple applications to the same company.

Examples:
- Branch `apply/matter-inc-sr-software-developer` → folder `matter-inc-sr-software-developer/`
- Branch `apply/okanagan-college-ai-developer-P02688` → folder `okanagan-college-ai-developer-P02688/`

**Minimum file set** — every application folder has these:

```
<folder>/
├── Resume.md
├── Resume.pdf
├── cover_letter.md
├── cover_letter.pdf
├── job_description.md
└── STATUS.md
```

**Extensibility** — add ad-hoc files when relevant, no enforced ceiling:

- `references.md`, `references.pdf` — only if the job requires references up front
- `Resume.html`, `cover_letter.html` — only if an HTML version is needed
- `take_home/` — coding assignments
- `interview_notes.md` — running notes across interview stages
- `portfolio_links.md`, recruiter emails, etc.

### STATUS.md format

Every application folder has a `STATUS.md`:

```markdown
# <Company> — <Role>

- **Job ID:** P02688 (or N/A)
- **Source:** Indeed / LinkedIn / Referral / Direct
- **Applied:** YYYY-MM-DD
- **Status:** applied | interviewing | won | closed
- **Last activity:** YYYY-MM-DD
- **Contact:** Name, role — email (or N/A)

## Timeline
- YYYY-MM-DD — Submitted application via <source>

## Notes
<free-form — recruiter conversations, interview feedback, salary discussion, reason closed, etc.>
```

The branch prefix gives quick dashboard visibility (`git branch` shows all). `STATUS.md` captures the narrative that doesn't fit in a branch name.

### Lifecycle

1. **Prospect** — job description dropped into `prospects/<slug>/` (gitignored). No branch.
2. **Start applying** — `git checkout -b apply/<slug>` off master. Create folder matching branch suffix. Commit tailored resume + cover letter + `job_description.md` + `STATUS.md`.
3. **Heard back / interview** — `git branch -m apply/<slug> interview/<slug>`. Update `STATUS.md` timeline. Optionally force-push to origin if remote tracking branch exists.
4. **Outcome** — rename to `won/<slug>` or `closed/<slug>`. Final `STATUS.md` entry captures the result and reason.
5. **Archive** — `closed/<slug>` branches can be deleted after ~90 days at the user's discretion. Branch history is preserved in git reflog for recovery if needed.

### Rules invariants

- Master never contains application folders.
- Branches are never merged into master. They live on their own branch forever (or until deleted during archive).
- Folder name always equals the branch suffix (everything after `<status>/`).
- Job description files are named `job_description.md`, never `job_desc.txt.txt` or similar.
- `prospects/`, `.firecrawl/`, and `.claude/` are gitignored.

## One-time cleanup migration

This section describes the actions required to bring the current repo into alignment. Will be executed via the implementation plan.

| # | Action | Branch |
|---|--------|--------|
| 1 | Add `.gitignore` entries: `/prospects/`, `/.firecrawl/`, `/.claude/` | master |
| 2 | Commit `Resume.pdf` regeneration | master |
| 3 | `git rm -r RentalHunt/` and commit | master |
| 4 | Delete untracked `Helm/` and `HillsdaleInvestmentManagement/` (user decided to drop these applications) | master working tree |
| 5 | Rename `apply/P02688-okanagan-college-ai-developer` → `apply/okanagan-college-ai-developer-P02688`, force-push to origin | branch |
| 6 | On `apply/okanagan-college-web-developer-P00181`: import untracked `P00181/` contents as folder `okanagan-college-web-developer-P00181/`, rename `job_desc.txt.txt` → `job_description.md`, add `STATUS.md` template | branch |
| 7 | On each existing `apply/*` branch: ensure folder name matches branch suffix; add `STATUS.md` if missing; rename any `job_description.txt.txt` → `job_description.md` | per branch |
| 8 | Delete untracked `P00181/` from master working tree after step 6 migration confirmed | master working tree |
| 9 | Push renamed and updated branches to origin | all |

Post-migration branch state:

- `apply/okanagan-college-ai-developer-P02688` (renamed)
- `apply/okanagan-college-web-developer-P00181`
- `apply/analytic-labs-ai-engineer`
- `apply/rentalhunt-software-developer`

## Out of scope

- Automation (scripts to create new application branches). Manual workflow is fine for the user's volume.
- Resume content changes. This design is about structure only.
- Syncing generic-resume updates into tailored branches. User does that ad hoc when tailoring.

## Decisions recap

| # | Question | Choice |
|---|----------|--------|
| Q1 | Source-of-truth model | A — branch-only; master stays generic |
| Q2 | Branch naming | B — `apply/<company>-<role>[-<id>]` |
| Q3 | Folder name inside branch | A — matches branch suffix exactly |
| Q4 | File set per application | C — minimal floor, extensible |
| Q5 | Status tracking | D — branch prefix + per-folder `STATUS.md` |
| Q6 | Un-started apps (Helm, Hillsdale) | B — gitignored `prospects/`; delete Helm/Hillsdale |
