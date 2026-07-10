# Assignment 5 — Connecting Claude to the Outside World

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will connect Claude Code to external systems using MCP (Model Context Protocol). You will configure the GitHub MCP server, securely store credentials, verify the connection, and run a live query that proves Claude is accessing real-time GitHub data.

---

# Task 1 — Create a GitHub Personal Access Token

## Goal

Generate a GitHub Personal Access Token (PAT) that will be used for MCP authentication:

**What I did**

I created a GitHub Personal Access Token (PAT) to securely authenticate the GitHub MCP server with my GitHub account. I configured the token with the required repo and read:user permissions and kept the token private by ensuring it was never exposed in any screenshots or shared publicly.

### Evidence

#### Screenshot 1 — GitHub token creation page showing the selected scopes (`repo`, `read:user`) — token value must NOT be visible

[Github PAT Creation Page](screenshots/GitHub-token-creation-page.PNG)



# Task 2 — Create .mcp.json at the Project Root

## Goal

Create and configure the `.mcp.json` file to define the GitHub MCP server:

**What I did**

I created the .mcp.json file in the project root and configured the GitHub MCP server. This file serves as the shared project configuration that tells Claude Code how to connect to GitHub through the Model Context Protocol whenever the project is opened.

### Evidence

#### Screenshot 2 — `.mcp.json` open in VS Code showing the full configuration

[.mcp.json configuration](screenshots/mcp.json-open-in-VSCode.PNG)


# Task 3 — Add Your Token to settings.local.json

## Goal

Store your GitHub token securely in `.claude/settings.local.json` and ensure it is not committed to version control:

**What I did**

I created the .claude/settings.local.json file to securely store my GitHub Personal Access Token and enabled the GitHub MCP server for the project. I also updated my .gitignore file to exclude this local configuration from version control, ensuring that my personal credentials remain private and are never committed to GitHub.

### Evidence

#### Screenshot 3 — `settings.local.json` open in VS Code showing the `env` section — **blur or cover the actual GitHub token value**

[settings.local.json](screenshots/settings.local.json-open-in-VSCode.PNG)



# Task 4 — Verify the Connection with /mcp

## Goal

Confirm that the GitHub MCP server is successfully connected inside Claude Code:

**What I did**

After restarting Claude Code to load the new MCP configuration, I verified the connection by running the /mcp command. The GitHub MCP server showed a connected status with all available tools, confirming that Claude Code could successfully communicate with my GitHub account through the Model Context Protocol.

### Evidence

#### Screenshot 4 — `/mcp` output showing `github: connected`

[mcp output GitHub connected](screenshots/MCP-output-showing-github-connected.png)



# Task 5 — Run a Live GitHub Query

## Goal

Verify MCP functionality by retrieving real-time data from your GitHub account using Claude Code:

**What I did**

I tested the GitHub MCP integration by asking Claude Code to list all my GitHub repositories. Claude retrieved my actual repositories directly from GitHub, including repository names, visibility, fork status, and recent activity. This confirmed that Claude was accessing live data through MCP instead of relying on its training data.

### Evidence

#### Screenshot 5 — Claude's response showing the GitHub MCP tool call and the retrieved README.md content.

[Real Github Repo pulled by Claude MCP](screenshots/My-Real-GitHub-repositories-Called-by-Claude-Using-MCP.png)


# Submission Instructions

- Ensure `.mcp.json` is committed to your GitHub repository
- Ensure `.claude/settings.local.json` is NOT committed (must be gitignored)
- Confirm token value is hidden in all screenshots
- Add all required screenshots to your submission
- Push final changes to your forked repository

---

## GitHub Repository URL

Paste your forked repository URL here:

[Forked Rep URL](https://github.com/kakpoklebervincent/Ultimate-Agentic-DevOps-with-Claude-Code)



## Security Confirmation

Confirm below:

- [ ] `settings.local.json` is added to `.gitignore`
- [ ] GitHub token is NOT exposed in repository or screenshots



# Completion Checklist

- [ ] GitHub PAT created with correct scopes (`repo`, `read:user`)
- [ ] `.mcp.json` created at project root
- [ ] `.claude/settings.local.json` contains token (hidden in screenshot)
- [ ] `.claude/settings.local.json` is NOT committed
- [ ] `/mcp` shows GitHub connection as active
- [ ] Live GitHub query returns real repository data
- [ ] All required screenshots added
- [ ] GitHub repository URL included

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