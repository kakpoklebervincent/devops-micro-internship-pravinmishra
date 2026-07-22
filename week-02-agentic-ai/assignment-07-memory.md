# Assignment 7 — A Claude That Remembers

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will explore Claude Code’s memory system. You will locate the project memory file, store structured information into it, restart your session, and verify that Claude can recall stored knowledge across sessions without being prompted again.

---

# Task 1 — Find the Memory File Location

## Goal

Discover exactly where Claude Code stores memory for this project.

**What I did**

I asked Claude where project memory is stored. Claude explained that project memories are saved outside the project repository inside the local .claude/projects directory. It also showed that each project has its own memory folder containing a MEMORY.md index file and individual memory files used to persist knowledge across sessions.
### Evidence

#### Screenshot 1 — Memory file path shown by Claude

[Memory File Location](screenshots/01-memory-file-location.png)



# Task 2 — Give Claude Information to Remember

## Goal

Teach Claude three specific facts about the project and instruct it to save them to the memory file.

**What I did**

I instructed Claude to permanently remember three project rules for this repository. The first rule defined the required hero section background color. The second rule instructed Claude not to use JavaScript unless I explicitly approve it. The third rule required Terraform formatting to be performed before running a Terraform plan. Claude confirmed that these rules had been successfully saved into the project's memory.


### Evidence

#### Screenshot 2 — Claude confirming the memory was saved

[Memory Saved Confirmation](screenshots/02-memory-saved-confirmation.png)

#### Screenshot 3 — The `MEMORY.md` file open in VS Code showing the saved content

[Project MEMORY.md](screenshots/03-memory-md-content.png)



# Task 3 — Close the Session Completely

## Goal

Terminate the current Claude Code session and restart it to ensure memory is the only persistent context source.

**What I did**

I completely closed the active Claude Code session and restarted VS Code before opening a new Claude Code session. This ensured there was no previous conversation history available, allowing me to verify that Claude's responses were coming entirely from the stored project memory.

### Evidence

#### Screenshot 4 — VS Code reopened with a fresh Claude Code session showing no previous conversation

[Fresh Claude Session](screenshots/04-fresh-claude-session.png)



# Task 4 — Prove Memory Recall Across Sessions

## Goal

Run three tests that prove Claude remembers what you told it — without you saying it again in the new session.

**What I did**

After restarting Claude Code, I asked questions about the project without reminding Claude of any previously stored information. Claude correctly recalled the required hero section background color from memory. I also requested that JavaScript be added to the landing page, and Claude followed the stored project rule by refusing to proceed without my explicit approval. This confirmed that the project memory persisted successfully across sessions.


### Evidence

#### Screenshot 5 — Claude recalling hero section colors

[Memory Recall - Hero Color](screenshots/05-memory-recall-hero-color.png)

#### Screenshot 6 — Claude refusing JavaScript request based on memory rule

[Memory Rule - JavaScript Restriction](screenshots/06-memory-rule-javascript.png)


# Submission Instructions

- Ensure memory was successfully saved into `MEMORY.md`
- Restart Claude Code session completely before testing recall
- Add all required screenshots to your GitHub repository
- Push final changes to your forked repository

---

## Linkedin Post Link

Paste your Linkedin post link here:

[Memory.md Linkedin Post](https://lnkd.in/p/eSSVdeHC)

## GitHub Repository URL

Paste your forked repository URL here:

[Forked Repo URL](https://github.com/kakpoklebervincent/Ultimate-Agentic-DevOps-with-Claude-Code)

---

# Completion Checklist

- [ ] Memory file path identified (Screenshot 1)
- [ ] Memory successfully saved via prompt (Screenshot 2)
- [ ] `MEMORY.md` shows stored content (Screenshot 3)
- [ ] Fresh session opened after full restart (Screenshot 4)
- [ ] Claude recalled hero colors correctly (Screenshot 5)
- [ ] Claude refused JavaScript request based on memory (Screenshot 6)
- [ ] All screenshots added and committed to GitHub repo
- [ ] Linkedin post created.

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*