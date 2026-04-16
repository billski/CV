# CV Repo Cleanup Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Bring `D:/code/cv` into alignment with the application-tracking design at `docs/superpowers/specs/2026-04-16-cv-application-tracking-design.md`.

**Architecture:** Two phases. Phase 1 cleans master (gitignore, remove `RentalHunt/`, commit `Resume.pdf`, delete abandoned prospect folders). Phase 2 migrates each existing `apply/*` branch so folder-name equals branch-suffix, adds a `STATUS.md`, normalizes `job_description.md`, and renames one mismatched branch.

**Tech Stack:** Git, bash (Windows Git Bash). No TDD — this is a repository-restructuring task; verification is via `git status`, `git log`, and `git ls-tree`.

**Pre-flight assumptions (verify before starting):**
- Current branch is `master`.
- `Resume.pdf` is modified but uncommitted (✓ confirmed).
- Untracked on master: `.claude/`, `.firecrawl/`, `Helm/`, `HillsdaleInvestmentManagement/`, `P00181/`, `docs/income-strategy-2026.md`.
- Remote tracking branches exist for `apply/P02688-okanagan-college-ai-developer` and `apply/rentalhunt-software-developer`.

---

## Phase 1 — Master cleanup

### Task 1: Update `.gitignore`

**Files:**
- Modify: `D:/code/cv/.gitignore`

- [ ] **Step 1: Read current `.gitignore`**

Run:
```bash
cd /d/code/cv && cat .gitignore
```
Expected output:
```
# Build / generator scripts (not needed for viewing CV)
md_to_pdf.py
Resume.html
CV_TEMPLATE.md
```

- [ ] **Step 2: Append new ignore rules**

Append these lines to `.gitignore`:
```
# Job-tracking system
/prospects/
/.firecrawl/
/.claude/
```

- [ ] **Step 3: Verify ignore rules take effect**

Run:
```bash
cd /d/code/cv && git status --short
```
Expected: `.claude/` and `.firecrawl/` no longer listed as untracked. (They must disappear; `Resume.pdf`, `Helm/`, `HillsdaleInvestmentManagement/`, `P00181/`, `docs/income-strategy-2026.md` should still appear.)

- [ ] **Step 4: Commit**

Run:
```bash
cd /d/code/cv && git add .gitignore && git commit -m "Ignore prospects/, .firecrawl/, .claude/

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

---

### Task 2: Delete abandoned prospect folders

The user decided to drop the Helm and Hillsdale applications.

**Files:**
- Delete: `D:/code/cv/Helm/`
- Delete: `D:/code/cv/HillsdaleInvestmentManagement/`

- [ ] **Step 1: Confirm both folders are fully untracked**

Run:
```bash
cd /d/code/cv && git ls-files Helm/ HillsdaleInvestmentManagement/
```
Expected: empty output (neither folder has tracked files).

- [ ] **Step 2: Delete the folders**

Run:
```bash
cd /d/code/cv && rm -rf Helm HillsdaleInvestmentManagement
```

- [ ] **Step 3: Verify gone from `git status`**

Run:
```bash
cd /d/code/cv && git status --short
```
Expected: no mention of `Helm/` or `HillsdaleInvestmentManagement/`. No commit needed — they were never tracked.

---

### Task 3: Remove committed `RentalHunt/` from master

`RentalHunt/Resume.html` is tracked on master and violates the design rule (master must be generic-only).

**Files:**
- Delete: `D:/code/cv/RentalHunt/` (tracked)

- [ ] **Step 1: Verify the folder's tracked contents**

Run:
```bash
cd /d/code/cv && git ls-files RentalHunt/
```
Expected output:
```
RentalHunt/Resume.html
```

- [ ] **Step 2: Remove the folder from tracking**

Run:
```bash
cd /d/code/cv && git rm -r RentalHunt
```
Expected output includes: `rm 'RentalHunt/Resume.html'`.

- [ ] **Step 3: Commit the removal**

Run:
```bash
cd /d/code/cv && git commit -m "Remove RentalHunt/ from master

RentalHunt content belongs on apply/rentalhunt-software-developer branch,
per cv-application-tracking design.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 4: Confirm gone**

Run:
```bash
cd /d/code/cv && ls RentalHunt 2>/dev/null; echo "exit=$?"
```
Expected: `exit=2` (folder no longer exists).

---

### Task 4: Commit the modified `Resume.pdf`

`Resume.pdf` was regenerated on master but not committed.

**Files:**
- Modify: `D:/code/cv/Resume.pdf`

- [ ] **Step 1: Verify the modification is present**

Run:
```bash
cd /d/code/cv && git status --short Resume.pdf
```
Expected:
```
 M Resume.pdf
```

- [ ] **Step 2: Commit**

Run:
```bash
cd /d/code/cv && git add Resume.pdf && git commit -m "Regenerate Resume.pdf

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 3: Verify master tree is clean (except the untracked P00181/ and docs/income-strategy-2026.md)**

Run:
```bash
cd /d/code/cv && git status --short
```
Expected output:
```
?? P00181/
?? docs/income-strategy-2026.md
```
(`P00181/` is retained — it's the data source for Task 5.)

---

## Phase 2 — Branch migration

### Task 5: Migrate `apply/okanagan-college-web-developer-P00181`

Import the untracked `P00181/` folder from master into this branch as `okanagan-college-web-developer-P00181/`, rename the job description file, and add `STATUS.md`.

**Files:**
- Create: `okanagan-college-web-developer-P00181/Resume.html`
- Create: `okanagan-college-web-developer-P00181/Resume.md`
- Create: `okanagan-college-web-developer-P00181/Resume.pdf`
- Create: `okanagan-college-web-developer-P00181/cover_letter.html`
- Create: `okanagan-college-web-developer-P00181/cover_letter.md`
- Create: `okanagan-college-web-developer-P00181/cover_letter.pdf`
- Create: `okanagan-college-web-developer-P00181/job_description.md` (renamed from `job_desc.txt.txt`)
- Create: `okanagan-college-web-developer-P00181/references.html`
- Create: `okanagan-college-web-developer-P00181/references.md`
- Create: `okanagan-college-web-developer-P00181/references.pdf`
- Create: `okanagan-college-web-developer-P00181/STATUS.md`

- [ ] **Step 1: Checkout the branch** (untracked `P00181/` carries along)

Run:
```bash
cd /d/code/cv && git checkout apply/okanagan-college-web-developer-P00181
```
Expected: checkout succeeds; `P00181/` still present on disk.

- [ ] **Step 2: Rename the folder to match branch suffix**

Run:
```bash
cd /d/code/cv && mv P00181 okanagan-college-web-developer-P00181
```
(Plain `mv`, not `git mv` — the folder is not tracked yet.)

- [ ] **Step 3: Rename the job description file**

Run:
```bash
cd /d/code/cv && mv okanagan-college-web-developer-P00181/job_desc.txt.txt okanagan-college-web-developer-P00181/job_description.md
```

- [ ] **Step 4: Create `STATUS.md`**

Write the file `okanagan-college-web-developer-P00181/STATUS.md` with the following content. Replace `<TODO>` tokens during the user-review phase; for now leave them as literal placeholders that the user will fill in.

```markdown
# Okanagan College — Web Developer (P00181)

- **Job ID:** P00181
- **Source:** <TODO: Okanagan College careers site / Indeed / other>
- **Applied:** <TODO: YYYY-MM-DD>
- **Status:** applied
- **Last activity:** <TODO: YYYY-MM-DD>
- **Contact:** <TODO or N/A>

## Timeline
- <TODO: YYYY-MM-DD> — Submitted application

## Notes
```

- [ ] **Step 5: Stage and commit**

Run:
```bash
cd /d/code/cv && git add okanagan-college-web-developer-P00181/ && git commit -m "Import tailored P00181 content into branch folder

Migrates untracked P00181/ from master into the tailored folder
for this application. Renames job_desc.txt.txt to job_description.md
and adds STATUS.md per the cv-application-tracking design.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 6: Verify folder matches the design's minimum set**

Run:
```bash
cd /d/code/cv && git ls-tree --name-only HEAD okanagan-college-web-developer-P00181/
```
Expected: 11 entries including `Resume.md`, `Resume.pdf`, `cover_letter.md`, `cover_letter.pdf`, `job_description.md`, `STATUS.md`.

- [ ] **Step 7: Push (new tracking branch)**

Run:
```bash
cd /d/code/cv && git push -u origin apply/okanagan-college-web-developer-P00181
```
Expected: branch pushed, tracking set.

---

### Task 6: Migrate `apply/rentalhunt-software-developer`

Rename `RentalHunt/` to `rentalhunt-software-developer/`, rename `JobDetails` to `job_description.md`, add `STATUS.md`.

**Files:**
- Rename: `RentalHunt/` → `rentalhunt-software-developer/`
- Rename: `rentalhunt-software-developer/JobDetails` → `rentalhunt-software-developer/job_description.md`
- Create: `rentalhunt-software-developer/STATUS.md`

- [ ] **Step 1: Checkout the branch**

Run:
```bash
cd /d/code/cv && git checkout apply/rentalhunt-software-developer
```

- [ ] **Step 2: Rename the folder**

Run:
```bash
cd /d/code/cv && git mv RentalHunt rentalhunt-software-developer
```

- [ ] **Step 3: Rename the job description file**

Run:
```bash
cd /d/code/cv && git mv rentalhunt-software-developer/JobDetails rentalhunt-software-developer/job_description.md
```

- [ ] **Step 4: Create `STATUS.md`**

Write `rentalhunt-software-developer/STATUS.md` with:

```markdown
# RentalHunt — Software Developer

- **Job ID:** N/A
- **Source:** Indeed
- **Applied:** <TODO: YYYY-MM-DD>
- **Status:** applied
- **Last activity:** <TODO: YYYY-MM-DD>
- **Contact:** <TODO or N/A>

## Timeline
- <TODO: YYYY-MM-DD> — Submitted application via Indeed

## Notes
```

- [ ] **Step 5: Stage and commit**

Run:
```bash
cd /d/code/cv && git add rentalhunt-software-developer/ && git commit -m "Rename RentalHunt/ to branch-suffix folder; normalize file names

Per the cv-application-tracking design, folder name must equal the
branch suffix and the job description file must be job_description.md.
Adds STATUS.md template.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 6: Force-push (rewrote history via rename)**

Run:
```bash
cd /d/code/cv && git push --force-with-lease origin apply/rentalhunt-software-developer
```
Expected: push succeeds. (`--force-with-lease` is safer than `--force`; it refuses the push if origin moved.)

---

### Task 7: Migrate `apply/analytic-labs-ai-engineer`

Rename `AI_Engineer/` to `analytic-labs-ai-engineer/`, rename `job_desc.txt.txt` to `job_description.md`, add `STATUS.md`.

This branch is local-only (not on origin) so no push is needed.

**Files:**
- Rename: `AI_Engineer/` → `analytic-labs-ai-engineer/`
- Rename: `analytic-labs-ai-engineer/job_desc.txt.txt` → `analytic-labs-ai-engineer/job_description.md`
- Create: `analytic-labs-ai-engineer/STATUS.md`

- [ ] **Step 1: Checkout the branch**

Run:
```bash
cd /d/code/cv && git checkout apply/analytic-labs-ai-engineer
```

- [ ] **Step 2: Rename the folder**

Run:
```bash
cd /d/code/cv && git mv AI_Engineer analytic-labs-ai-engineer
```

- [ ] **Step 3: Rename the job description file**

Run:
```bash
cd /d/code/cv && git mv analytic-labs-ai-engineer/job_desc.txt.txt analytic-labs-ai-engineer/job_description.md
```

- [ ] **Step 4: Create `STATUS.md`**

Write `analytic-labs-ai-engineer/STATUS.md` with:

```markdown
# Analytic Labs — AI Engineer

- **Job ID:** N/A
- **Source:** <TODO>
- **Applied:** <TODO: YYYY-MM-DD>
- **Status:** applied
- **Last activity:** <TODO: YYYY-MM-DD>
- **Contact:** <TODO or N/A>

## Timeline
- <TODO: YYYY-MM-DD> — Submitted application

## Notes
```

- [ ] **Step 5: Stage and commit**

Run:
```bash
cd /d/code/cv && git add analytic-labs-ai-engineer/ && git commit -m "Rename AI_Engineer/ to branch-suffix folder; normalize file names

Per the cv-application-tracking design. Adds STATUS.md template.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 6: Verify folder contents**

Run:
```bash
cd /d/code/cv && git ls-tree --name-only HEAD analytic-labs-ai-engineer/
```
Expected: `Resume.html`, `Resume.md`, `cover_letter.html`, `cover_letter.md`, `job_description.md`, `references.html`, `references.md`, `STATUS.md`. (PDFs absent — user can generate later if re-applying.)

---

### Task 8: Rename + migrate `apply/P02688-okanagan-college-ai-developer`

This branch has two problems: (a) the P-code is in the prefix position, (b) tailored resume files are at the repo root instead of in a folder, and the folder `P02688/` only contains `cover_letter.md`.

**Target branch name:** `apply/okanagan-college-ai-developer-P02688`
**Target folder:** `okanagan-college-ai-developer-P02688/`

**Files:**
- Move: root `Resume.md`, `Resume.html`, `Resume.pdf` → `okanagan-college-ai-developer-P02688/`
- Move: `P02688/cover_letter.md` → `okanagan-college-ai-developer-P02688/cover_letter.md`
- Delete: `P02688/` (now-empty folder)
- Restore: root `Resume.md`, `Resume.html`, `Resume.pdf` from master
- Create: `okanagan-college-ai-developer-P02688/STATUS.md`

- [ ] **Step 1: Checkout the old branch**

Run:
```bash
cd /d/code/cv && git checkout apply/P02688-okanagan-college-ai-developer
```

- [ ] **Step 2: Rename the branch locally**

Run:
```bash
cd /d/code/cv && git branch -m apply/P02688-okanagan-college-ai-developer apply/okanagan-college-ai-developer-P02688
```
Expected: branch renamed; you're now on `apply/okanagan-college-ai-developer-P02688`.

Verify:
```bash
cd /d/code/cv && git branch --show-current
```
Expected: `apply/okanagan-college-ai-developer-P02688`.

- [ ] **Step 3: Create the target folder and move tailored files into it**

Run:
```bash
cd /d/code/cv && mkdir -p okanagan-college-ai-developer-P02688 && git mv Resume.md okanagan-college-ai-developer-P02688/Resume.md && git mv Resume.html okanagan-college-ai-developer-P02688/Resume.html && git mv Resume.pdf okanagan-college-ai-developer-P02688/Resume.pdf && git mv P02688/cover_letter.md okanagan-college-ai-developer-P02688/cover_letter.md
```

- [ ] **Step 4: Remove the now-empty `P02688/` folder**

Run:
```bash
cd /d/code/cv && rmdir P02688
```
Expected: succeeds (directory is empty after the `git mv`).

- [ ] **Step 5: Restore root resume files from master (so root is generic again)**

Run:
```bash
cd /d/code/cv && git checkout master -- Resume.md Resume.html Resume.pdf
```
Expected: three files restored at repo root with generic content from master's HEAD.

- [ ] **Step 6: Create `STATUS.md`**

Write `okanagan-college-ai-developer-P02688/STATUS.md` with:

```markdown
# Okanagan College — AI / Automation Solutions Developer (P02688)

- **Job ID:** P02688
- **Source:** Okanagan College careers site
- **Applied:** <TODO: YYYY-MM-DD>
- **Status:** applied
- **Last activity:** <TODO: YYYY-MM-DD>
- **Contact:** <TODO or N/A>

## Timeline
- <TODO: YYYY-MM-DD> — Submitted application

## Notes
Part-time / on-call role. Tailored resume emphasizes AI-accelerated development
and automation experience (see Resume.md in this folder).
```

- [ ] **Step 7: Stage and commit the restructure**

Run:
```bash
cd /d/code/cv && git add okanagan-college-ai-developer-P02688/ Resume.md Resume.html Resume.pdf && git commit -m "Restructure to branch-suffix folder; restore generic root files

Moves tailored Resume.{md,html,pdf} from repo root and cover_letter.md
from P02688/ into okanagan-college-ai-developer-P02688/. Root Resume.*
restored from master so the branch root remains generic per design.
Adds STATUS.md template.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>"
```

- [ ] **Step 8: Verify folder contents**

Run:
```bash
cd /d/code/cv && git ls-tree --name-only HEAD okanagan-college-ai-developer-P02688/
```
Expected: `Resume.html`, `Resume.md`, `Resume.pdf`, `cover_letter.md`, `STATUS.md`. (No `cover_letter.pdf` or `job_description.md` — these weren't captured originally; user can add if re-applying.)

- [ ] **Step 9: Push new branch name**

Run:
```bash
cd /d/code/cv && git push -u origin apply/okanagan-college-ai-developer-P02688
```
Expected: new branch pushed.

- [ ] **Step 10: Delete the old branch on origin**

Run:
```bash
cd /d/code/cv && git push origin :apply/P02688-okanagan-college-ai-developer
```
Expected: remote ref deleted.

---

## Phase 3 — Final verification

### Task 9: Confirm final repo state

- [ ] **Step 1: Return to master**

Run:
```bash
cd /d/code/cv && git checkout master
```
Expected: checkout succeeds; working tree clean except for `docs/income-strategy-2026.md` (which is outside the scope of this cleanup).

- [ ] **Step 2: Verify master contains no application folders**

Run:
```bash
cd /d/code/cv && ls -d */ | grep -v '^docs/$'
```
Expected: empty output (no application folders at master root).

- [ ] **Step 3: Verify branch list matches the design**

Run:
```bash
cd /d/code/cv && git branch --list 'apply/*'
```
Expected:
```
  apply/analytic-labs-ai-engineer
  apply/okanagan-college-ai-developer-P02688
  apply/okanagan-college-web-developer-P00181
  apply/rentalhunt-software-developer
```
(Note: no `apply/P02688-*` anymore.)

- [ ] **Step 4: Verify each branch has folder=suffix**

Run:
```bash
cd /d/code/cv && for b in apply/analytic-labs-ai-engineer apply/okanagan-college-ai-developer-P02688 apply/okanagan-college-web-developer-P00181 apply/rentalhunt-software-developer; do suffix="${b#apply/}"; echo "$b → folder check:"; git ls-tree -d --name-only "$b" | grep -E "^${suffix}$" && echo "  ✓ $suffix/ exists" || echo "  ✗ missing $suffix/"; done
```
Expected: all four branches print `✓ <suffix>/ exists`.

- [ ] **Step 5: Verify each branch has STATUS.md in its folder**

Run:
```bash
cd /d/code/cv && for b in apply/analytic-labs-ai-engineer apply/okanagan-college-ai-developer-P02688 apply/okanagan-college-web-developer-P00181 apply/rentalhunt-software-developer; do suffix="${b#apply/}"; git ls-tree --name-only "$b" "$suffix/STATUS.md" | grep -q "STATUS.md" && echo "$b ✓" || echo "$b ✗ missing STATUS.md"; done
```
Expected: all four print `✓`.

- [ ] **Step 6: Remote tracking check**

Run:
```bash
cd /d/code/cv && git branch -vv | grep apply
```
Expected: all four `apply/*` branches show `[origin/apply/...]` tracking. No stale reference to `apply/P02688-...`.

---

## Out of scope (not part of this plan)

- Regenerating missing PDFs on old branches. User can do that if they re-apply.
- Committing `docs/income-strategy-2026.md` — separate decision.
- Creating `prospects/` directory. It's gitignored; user creates it when they first drop a prospect in.
- Changing existing commit history on closed apps. Rename/move operations naturally rewrite history on the affected branches (and are force-pushed where origin copies exist).
