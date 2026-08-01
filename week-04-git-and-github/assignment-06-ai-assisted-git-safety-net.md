# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2, I built Claude Code hooks that block a dangerous action before it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). This assignment showed me that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

I built both halves of a real "PR Ready" workflow: a Git hook that follows fixed rules and refuses a commit outright when it finds a hardcoded secret or an oversized file, and a restricted Claude Code skill (`/pr-ready`) that reads the staged diff and drafts a Pull Request title, description, and a short list of things worth a second look, the kind of judgment a fixed rule can't make. The skill never commits, pushes, or opens the PR, I do that myself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only I act.

A note on which repository this assignment uses: the assignment brief text repeatedly said `devops-micro-internship-pravinmishra`, but the official solution walkthrough confirmed this assignment actually continues in `devops-micro-internship-interviews`, the same fork I set up in Assignment 5. I verified this against the solution doc before proceeding, since the brief text appears to have had a copy-paste inconsistency.

---

# Task 0 — Confirm Your Fork and Create a Feature Branch

## Goal

Confirm I'm working in my own fork, then create a dedicated branch for this assignment.

I moved into my local clone of `devops-micro-internship-interviews`, confirmed my remotes were still correctly configured from Assignment 5, made sure `main` was up to date, then created a fresh branch dedicated to this assignment's work:

```bash
git branch
git remote -v
git checkout main
git pull origin main
git checkout -b feature/ai-pr-ready
git branch
```

* `git branch`: lists local branches and shows which one is currently active.
* `git remote -v`: confirms `origin` points to my fork and `upstream` points to `pravinmishraaws/devops-micro-internship-interviews`.
* `git checkout main`: switches to `main` before branching, so the new branch starts from a clean, current base.
* `git pull origin main`: pulls the latest changes from my fork's `main` into my local copy, in this case it reported "Already up to date."
* `git checkout -b feature/ai-pr-ready`: creates and switches to the new feature branch in one step.
* `git branch` (again): confirms `feature/ai-pr-ready` is now active, marked with `*`.

### Evidence

#### Screenshot 1 — Output of `git remote -v` and `git branch` showing the new branch

![Screenshot 1 – Fork Branch](screenshots/Week-04-Ass-06-Task-00-Fork-Branch.png)

---

### Notes

**1. Why create a dedicated branch instead of doing this work on `main`?**

Creating a dedicated branch keeps this assignment's work fully isolated from `main`. If the pre-commit hook or the Claude Code skill turned out broken or needed heavy iteration, `main` would stay untouched and always in a known-good state. It also means the eventual Pull Request contains only the files relevant to this assignment (`scripts/notify.sh`, `hooks/pre-commit`, `.claude/skills/pr-ready/SKILL.md`), not mixed in with unrelated history from other branches.

---

# Task 1 — Stage a Change With Realistic Risk

## Goal

Create `scripts/notify.sh` containing a fake secret and a debug statement, then stage it, so there's something realistic for both the hook and the AI skill to catch.

I created a `scripts` folder and, inside it, a small script deliberately written with two common security mistakes: a hardcoded value shaped like a real AWS access key, and a debug `echo` that would print that key if the script ran.

```bash
mkdir -p scripts
```

I created `scripts/notify.sh` with this content:

```bash
#!/bin/bash
# demo only — fake credential for this assignment, never a real key
AWS_ACCESS_KEY_ID=AKIAABCDEFGHIJKLMNOP
echo "DEBUG: token is $AWS_ACCESS_KEY_ID"
```

I then confirmed the file was untracked, staged it, and confirmed it moved into the staging area:

```bash
git status
git add scripts/notify.sh
git status
```

* `mkdir -p scripts`: creates a `scripts` folder, a common convention for utility/helper scripts.
* First `git status`: confirmed the new file appeared as untracked before staging.
* `git add scripts/notify.sh`: stages the file, this is the staged version the pre-commit hook actually scans against in Task 3.
* Second `git status`: confirmed it moved under "Changes to be committed."

### Evidence

#### Screenshot 2 — Output of `git status` showing the staged file on `feature/ai-pr-ready`

![Screenshot 2 – Staged File](screenshots/Week-04-Ass-06-Task-01-Staged-File.png)

---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

The key is clearly labeled in a comment as fake, and follows a pattern that isn't a real AWS credential, so that testing the detection logic never risks leaking an actual secret. If a genuine secret leaked to a public GitHub repo, it would remain discoverable in the commit history even after being removed in a later commit, unless the history itself is rewritten. Using a fake key lets the hook and the `/pr-ready` skill be tested safely and repeatedly with zero real security exposure.

---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

I created a `hooks` folder to hold a version-controlled hook, separate from Git's default untracked `.git/hooks/` directory, then wrote the hook script directly via heredoc to avoid the editor paste issues I'd run into on an earlier assignment:

```bash
mkdir -p hooks
```

```bash
cat > hooks/pre-commit << 'EOF'
#!/bin/bash
# hooks/pre-commit — blocks commits with likely secrets or oversized files
set -e
staged=$(git diff --cached --name-only --diff-filter=ACM)
blocked=0
for file in $staged; do
  if git diff --cached -- "$file" | grep -qE 'AKIA[0-9A-Z]{16}|-----BEGIN (RSA|OPENSSH|PRIVATE) KEY-----'; then
    echo "BLOCKED: possible secret in $file"
    blocked=1
  fi

  size=$(git cat-file -s "$(git rev-parse ":$file")" 2>/dev/null || echo 0)
  if [ "$size" -gt 1000000 ]; then
    echo "BLOCKED: $file is $(($size / 1000000))MB — over the 1MB limit"
    blocked=1
  fi
done

if [ "$blocked" -eq 1 ]; then
  echo "Commit rejected. Fix the issues above and try again."
  exit 1
fi
EOF
```

With the script written, I made it executable and pointed Git at the tracked `hooks/` folder instead of the default hidden one:

```bash
chmod +x hooks/pre-commit
git config core.hooksPath hooks
git config core.hooksPath
ls -la hooks/
```

* `mkdir -p hooks`: creates the folder that will hold the tracked, shareable hook.
* `cat > hooks/pre-commit << 'EOF' ... EOF`: writes the full script directly into the file via heredoc.
* `chmod +x hooks/pre-commit`: makes the script executable, without this, Git can't run it as a hook.
* `git config core.hooksPath hooks`: tells Git to look inside the tracked `hooks/` folder for its hooks, instead of the default untracked `.git/hooks/` folder.
* `git config core.hooksPath` (no value): prints back the current setting to confirm it's `hooks`.
* `ls -la hooks/`: confirms the file exists with executable permissions (`-rwxr-xr-x`).

**How the script itself works, piece by piece:**

* `#!/bin/bash`: the shebang line, tells the OS which program should run this script.
* `set -e`: exits immediately if any command fails, preventing the script from silently continuing past an unexpected error.
* `staged=$(git diff --cached --name-only --diff-filter=ACM)`: gathers the list of files about to be committed. `--cached` compares the staging area (not the working directory) against the last commit, this is specifically what's about to be committed. `--name-only` outputs just filenames. `--diff-filter=ACM` only includes files that are Added, Copied, or Modified, skipping deletions since there's nothing to scan in a deleted file.
* `blocked=0`: a flag starting at "nothing blocked yet," set to `1` later if a problem is found.
* `for file in $staged; do ... done`: loops through each staged filename, running both checks below on every file.
* **Secret check:** `git diff --cached -- "$file" | grep -qE 'AKIA[0-9A-Z]{16}|-----BEGIN (RSA|OPENSSH|PRIVATE) KEY-----'` pipes the file's staged content into `grep`, searching (quietly, using extended regex) for an AWS access key pattern (`AKIA` + 16 uppercase letters/digits) or a private key file header. A match sets `blocked=1` and prints which file triggered it.
* **Size check:** `git rev-parse ":$file"` gets the Git object ID for the staged version of the file, `git cat-file -s` returns that object's size in bytes. If it exceeds 1,000,000 bytes (1MB), it's blocked too.
* **Final decision:** if `blocked` was ever set to `1`, the script prints a rejection message and calls `exit 1`. This non-zero exit code is the actual mechanism that tells Git to abort the commit, it's not the `echo` messages doing the blocking, it's this exit code.

### Evidence

#### Screenshot 3 — `hooks/pre-commit` open in VS Code showing the full script

![Screenshot 3 – Hook Script](screenshots/Week-04-Ass-06-Task-02-Hook-Script.png)

---

#### Screenshot 4 — Output of `git config core.hooksPath` confirming it points to `hooks`

![Screenshot 4 – Hooks Path](screenshots/Week-04-Ass-06-Task-02-Hooks-Path.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**

`.git/hooks/` is local-only, it never gets pushed, cloned, or shared, so if I put the hook there, every teammate would have to write the exact same script themselves with zero enforcement that they actually did. By putting the script in a regular tracked folder (`hooks/`) and pointing `core.hooksPath` at it, the hook becomes part of the repository's actual codebase, versioned, reviewable, and shareable. Anyone who clones the repo gets the same script; they just need to run `git config core.hooksPath hooks` once to activate it (a real limitation I noted later when `/pr-ready` flagged it as a caveat for reviewers).

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

`PreToolUse` intercepts a Claude Code agent's *tool call* before it executes, for example, blocking a dangerous Bash command or file write before Claude actually runs it. This pre-commit hook intercepts a *Git commit* before it's created, running automatically the moment `git commit` is invoked. Both share the same underlying pattern: a fixed checkpoint that runs **before** an action is allowed to complete, inspects what's about to happen, and can refuse it outright based on rules, with no judgment or negotiation involved. Both are "gates," not reviewers, they say yes or no, nothing in between.

---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and confirm the hook rejects it.

With `scripts/notify.sh` still staged, containing the fake AWS key and debug echo, I attempted to commit it:

```bash
git commit -m "add notification script"
```

The pre-commit hook intercepted the commit before Git created it:

BLOCKED: possible secret in scripts/notify.sh
Commit rejected. Fix the issues above and try again.


No new commit was created, confirmed by the shell's non-zero exit indicator.

### Evidence

#### Screenshot 5 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

![Screenshot 5 – Blocked Commit](screenshots/Week-04-Ass-06-Task-03-Blocked-Commit.png)

---

### Notes

**1. Which line in `hooks/pre-commit` matched my fake key, and why did it match?**

The line `if git diff --cached -- "$file" | grep -qE 'AKIA[0-9A-Z]{16}|...'` matched. My fake key is `AKIAABCDEFGHIJKLMNOP`. The regex `AKIA[0-9A-Z]{16}` requires exactly `AKIA` followed by 16 uppercase letters or digits. Counting `ABCDEFGHIJKLMNOP` after `AKIA` gives exactly 16 characters, so it matched the pattern precisely, this is the real-world format of AWS Access Key IDs, which is why the hook targets it specifically.

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

No. This hook only matches specific known patterns, the `AKIA...` prefix for AWS keys and private key file headers. A secret stored in a plain variable like `mySecretToken = "xyz123..."` with no recognizable prefix or format would completely slip past it. This shows the core limitation of fixed-rule detection: it only catches what it's explicitly programmed to recognize. It has no understanding of *meaning*, only pattern matching, so any secret that doesn't match a known signature goes undetected.

---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads staged changes and produces a PR-readiness report and a draft PR description, without writing, committing, or pushing anything itself.

I created a `.claude/skills/pr-ready` folder, the location Claude Code expects for a project-level custom skill, and wrote its definition directly via heredoc:

```bash
mkdir -p .claude/skills/pr-ready
```

```bash
cat > .claude/skills/pr-ready/SKILL.md << 'EOF'
---
name: pr-ready
description: Reviews staged Git changes and drafts a PR title, description, and risk report. Never commits, pushes, or opens PRs.
allowed-tools: Bash, Read, Grep
disable-model-invocation: true
---

You are reviewing staged changes before a Pull Request is opened.
1. Run `git diff --cached` and `git status` to see exactly what is staged.
2. Report any of the following if present: secrets or credential-shaped
strings, debug print/echo statements, TODO/FIXME left in code, a diff
that mixes unrelated concerns, or a change with no corresponding notes.
3. Draft a PR title that starts with a short word like `feat:` or `fix:`
telling the reader what kind of change this is, and a 3-5 sentence PR
description explaining what changed and why.
4. Never run `git commit`, `git push`, or `gh pr create`. Never edit files.
Your output is a draft for a human to review and use.
EOF
```

The **frontmatter** (between the `---` lines) is what Claude Code actually reads to configure the skill: `allowed-tools: Bash, Read, Grep` explicitly grants only those three tools, no `Write` means the skill is structurally incapable of editing any file, not just instructed not to. `disable-model-invocation: true` means the skill only runs when I manually type `/pr-ready`, Claude can never decide to invoke it on its own initiative.

I started a fresh Claude Code session from the repo root, required for a newly added skill to be picked up:

```bash
claude
```

Then, with the risky `scripts/notify.sh` still staged, I ran the skill:

/pr-ready


The output correctly identified both issues, and went further than the hook could, flagging the credential-shaped string as High severity, the debug echo as a separate Medium-severity issue, plus two Low-severity notes about a missing trailing newline and missing documentation, along with a drafted PR title (`chore: add demo notify.sh script`) and a clear recommendation not to merge as-is.

### Evidence

#### Screenshot 6 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

![Screenshot 6 – Skill Frontmatter](screenshots/Week-04-Ass-06-Task-04-Skill-Frontmatter.png)

---

#### Screenshot 7 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

![Screenshot 7 – PR Ready Output](screenshots/Week-04-Ass-06-Task-04-PR-Ready-Output.png)

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

`Bash` lets the skill run read-only Git commands like `git diff --cached` and `git status` to gather evidence. `Read` lets it inspect file contents directly. Neither of these can modify anything, they only retrieve information. `Write` is deliberately excluded because this skill's entire purpose is advisory: it analyzes and drafts, but must never be able to alter the codebase. Without `Write`, there's a hard technical guarantee, not just a policy, that the skill can't accidentally edit `notify.sh` or any other file.

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

Both caught the fake AWS key. But `/pr-ready` went further, separately flagging the debug echo statement as its own medium-severity issue (something the hook doesn't check for at all, since it only scans for secret *patterns* and file size, not risky code behavior). `/pr-ready` also caught two things the hook has no concept of: missing documentation and a judgment call about whether the file should even exist in its current form. The hook is binary, block or allow, based on pattern matches. `/pr-ready` reasons about the code's actual purpose and communicates *why* something is a problem, which a fixed rule fundamentally can't do.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

I rewrote `scripts/notify.sh`, replacing the hardcoded key and debug echo with a harmless placeholder comment and a plain status message:

```bash
cat > scripts/notify.sh << 'EOF'
#!/bin/bash
# demo only - placeholder notification script, no credentials used
echo "Notification script running"
EOF
```

I staged and committed the fix:

```bash
git add scripts/notify.sh
git commit -m "add notification script"
```

This time the commit succeeded cleanly, no BLOCKED message:

[feature/ai-pr-ready 69fe1ff] add notification script
1 file changed, 3 insertions(+)


I then staged and committed `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md`, both were still untracked up to this point since I'd only written them to disk, never added them:

```bash
git add hooks/pre-commit .claude/skills/pr-ready/SKILL.md
git commit -m "add pre-commit secret hook and pr-ready skill"
```

To get a genuine clean `/pr-ready` run with something real staged (rather than an empty staging area), I temporarily undid that last commit without losing the staged content:

```bash
git reset --soft HEAD~1
```

* `git reset --soft HEAD~1`: moves the branch pointer back one commit, but keeps all the changes from that commit staged, exactly as they were right before committing. This is fully safe and reversible, nothing is lost, it's the opposite of `--hard`, which would have discarded the changes entirely.

With the hook and skill files staged again, I re-ran `/pr-ready` in Claude Code. This produced a genuinely clean risk report, no secrets, no debug statements, no TODOs, coherent scope, along with a drafted PR title: `feat: add pr-ready review skill and secret-blocking pre-commit hook`. It also caught a real, non-blocking observation on its own: `core.hooksPath` isn't itself version-controlled, so a teammate cloning the repo needs to run `git config core.hooksPath hooks` manually to activate the hook.

I then restored the commit exactly as it was:

```bash
git commit -m "add pre-commit secret hook and pr-ready skill"
git log --oneline -5
```

### Evidence

#### Screenshot 8 — `git commit` succeeding after the fix (no BLOCKED message)

![Screenshot 8 – Commit Success](screenshots/Week-04-Ass-06-Task-05-Commit-Success.png)

---

#### Screenshot 9 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

![Screenshot 9 – PR Ready Clean](screenshots/Week-04-Ass-06-Task-05-PR-Ready-Clean.png)

---

### Notes

**1. What exactly did I change to satisfy the pre-commit hook?**

I removed the hardcoded `AWS_ACCESS_KEY_ID=AKIAABCDEFGHIJKLMNOP` line and the `echo "DEBUG: token is $AWS_ACCESS_KEY_ID"` line from `scripts/notify.sh`, replacing them with a harmless placeholder comment and a plain status message (`echo "Notification script running"`). This removed both the secret-shaped string the hook's regex was matching and the debug statement `/pr-ready` had flagged, allowing the commit to pass cleanly through both gates.

---

# Task 6 — Push and Open a Pull Request Using the AI Draft

## Goal

Push the branch and open a Pull Request against my own fork (not upstream), using `/pr-ready`'s drafted title and description as a starting point, read critically and edited before use.

I pushed my feature branch to my fork:

```bash
git push origin feature/ai-pr-ready
```

GitHub responded with a direct PR creation link. On the compare page, I deliberately double-checked the **base repository** field, since GitHub defaults to remembering the last PR's target, which for me had been upstream (from Assignment 5). It had indeed defaulted to `pravinmishraaws/devops-micro-internship-interviews`, which was wrong for this assignment, this PR needed to stay entirely within my own fork.

I switched the base repository dropdown to `kakpoklebervincent/devops-micro-internship-interviews`, which collapsed the comparison to a same-fork comparison (`base: main` → `compare: feature/ai-pr-ready`), confirmed by the resulting URL:

github.com/kakpoklebervincent/devops-micro-internship-interviews/compare/main...kakpoklebervincent:devops-micro-internship-interviews:feature/ai-pr-ready


I replaced GitHub's auto-generated title (a generic conversion of the branch name) with the actual `/pr-ready` draft title, and replaced the repository's leftover PR template text (irrelevant boilerplate meant for a different kind of contribution) with the `/pr-ready` draft description, edited to include its `core.hooksPath` observation for reviewers.

With everything verified, I clicked **Create pull request**, creating **PR #1**, "Ready to merge," correctly targeting `main` on my own fork from `feature/ai-pr-ready`, with 2 commits and 3 files changed.

### Evidence

#### Screenshot 10 — My Pull Request showing the base repository is my own fork, plus the title and description

![Screenshot 10 – PR Created](screenshots/Week-04-Ass-06-Task-06-PR-Created.png)

---

#### PR Link

`https://github.com/kakpoklebervincent/devops-micro-internship-interviews/pull/1`

---

### Notes

**1. What, if anything, did I edit in the AI's drafted PR description before using it? Why?**

I kept the AI's Summary section largely as drafted, since it accurately described both files added. I incorporated its "Notes for Reviewers" observation about `core.hooksPath` not being version-controlled and needing manual setup, since that's a genuinely useful caveat for anyone reviewing or merging this PR, without it, a reviewer might assume the hook activates automatically on clone.

**2. If I had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

The title and description could have been technically accurate but missed something important, or in a worse case, could have been generated against the wrong staged content if I'd run `/pr-ready` at the wrong point in my workflow. Since I read it critically, I caught that the draft's observations were genuinely correct and useful rather than assuming they were. More broadly, copy-pasting an AI draft without review risks presenting something to reviewers as though a human had verified it, when actually no one had, undermining the whole point of a PR description being trustworthy.

**3. Why does this PR need to target my own fork instead of the shared upstream repository?**

This assignment's files (a test script with a fake key, a personal pre-commit hook, a personal Claude Code skill) are practice/demonstration artifacts specific to my own learning exercise, not a contribution meant for Pravin's shared class repository. Opening this against upstream would have proposed irrelevant, single-student practice files to a repo shared by the entire cohort. Keeping the PR within my own fork keeps the practice contained to my own repository while still letting me practice the full real PR workflow.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

Gather is where information is collected before any judgment is made. Both the pre-commit hook and `/pr-ready` do this. The hook runs `git diff --cached --name-only` to see which files are staged. `/pr-ready` gathers more broadly, running `git diff --cached` and `git status` to see exactly what changed and what's staged. At this point, neither tool is making a decision, they're just collecting facts.

**2. Which step(s) represent Analyze?**

Analyze is where that information gets evaluated. The pre-commit hook does rule-based analysis, checking staged content against fixed patterns like AWS key formats (`AKIA...`) or file size limits. Its output is predictable and deterministic. `/pr-ready` does context-aware analysis instead, it can spot things like leftover debug statements, missing documentation, or a diff that doesn't match its own PR description. Both are "Analyze," just at very different levels of sophistication, one is a pattern match, the other is reasoning.

**3. Which step is Human Act, and why must a human, not Claude, run `git commit`, `git push`, and open the Pull Request?**

Human Act covers every action that actually changes shared state: fixing `notify.sh`, committing, pushing, and opening the PR. I did all of these myself. This matters because these actions are largely irreversible once they hit GitHub, they affect collaborators and the shared history. AI can analyze and suggest, but it can also be wrong or miss context. Keeping a human in charge of every action that touches the shared repo means someone accountable actually reviewed and approved it first.

**4. Which step is Verify?**

Verify is confirming the fix actually worked, not assuming it did. I did this twice: re-running `git commit` after removing the secret and debug line (confirming the hook let it through cleanly), and re-running `/pr-ready` (confirming a clean risk report with no findings).

**5. Why do I need both the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

The hook is fast, deterministic, and can't be argued with, it's built for hard, non-negotiable rules, but it only catches what it's explicitly programmed to look for. The AI skill fills that gap by reasoning about context, catching things like debug statements or a mismatched PR description, but its judgment isn't guaranteed to be complete or correct, so it can never be trusted to act unsupervised. Together they cover two different failure modes: the hook guarantees a minimum floor of protection, and the AI adds broader judgment on top, without either one being asked to do the other's job.

---

# Task 8 — LinkedIn Post

## Evidence

#### LinkedIn Post URL

https://www.linkedin.com/feed/update/urn:li:activity:7486815065388752897

---

## Key Learnings

- A fixed-rule pre-commit hook and an AI-assisted review skill catch fundamentally different classes of problems, one guarantees consistent detection of known patterns, the other reasons about context and intent.
- Restricting a Claude Code skill's `allowed-tools` (no `Write`) creates a hard technical guarantee that it can't modify files, not just a behavioral instruction that could be ignored.
- `git reset --soft` is a safe way to temporarily unstage a commit's changes without losing them, useful for re-testing tools against staged content after already committing.
- GitHub remembers the last PR's base repository by default, always double-check the base repository field before creating a PR, especially when switching between contributing upstream and working within your own fork.
- Reading and editing an AI-drafted PR description before using it, rather than copy-pasting it, is what actually makes the description trustworthy to a reviewer.

---

# Submission Instructions

- `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` committed to my forked repository
- All written answers in my own words
- No real secret or credential used anywhere, the fake key stayed clearly fake throughout
- Pull Request opened against my own fork, not upstream

---

## GitHub Repository URL

`https://github.com/kakpoklebervincent/devops-micro-internship-interviews`

---

## Completion Checklist

- [x] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [x] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [x] `core.hooksPath` configured to point at `hooks/`
- [x] Pre-commit hook shown blocking the risky commit
- [x] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [x] `/pr-ready` run against the risky diff and shown flagging issues
- [x] Risky file fixed; `git commit` succeeds cleanly
- [x] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [x] Pull Request opened using the AI draft as a starting point, with my own fork as the base repository (not upstream), PR link included
- [x] Agentic Loop mapping (Task 7) completed in my own words
- [ ] LinkedIn post published and URL submitted


---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*

