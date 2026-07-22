# Assignment 4 — Building Your AI Team

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build and configure a set of specialized AI subagents inside your project. You will learn how different models and tool permissions define agent behavior, and you will trigger two real agent delegations to analyze security and cost aspects of your Terraform infrastructure.

---

# Task 1 — Create the Agents Folder and Add Files

## Goal

Create the `.claude/agents/` directory and add all required agent files:

**What I did**

I created the .claude/agents directory and added the three downloaded agent files to it. After placing the files in the correct location, I verified that all agent filenames matched the expected names so Claude Code could recognize and use each subagent correctly.

### Evidence

#### Screenshot 1 — VS Code sidebar showing `.claude/agents/` with all 3 files

[Agents folder structure](screenshots/SubAgents-folder-structure-in-VS%20Code.png)



# Task 2 — Compare the Agent Configurations

## Goal

Analyze the configuration differences between the three agents and demonstrate understanding of model and tool selection:

**What I did**

I reviewed the frontmatter configuration of each subagent to understand how their models and tool permissions were defined. By comparing the agents, I learned why different models and tool restrictions are used depending on the specific responsibility assigned to each subagent.

### Written Answers

#### 1. Why does the cost optimizer use Haiku instead of Sonnet?

Cost optimization is a pattern-matching task — it reads Terraform files and compares resource configurations against known cost rules. This doesn't require deep reasoning or complex analysis. Haiku is faster and cheaper, making it the right model for repetitive, structured checks. Using Sonnet here would be slower and more expensive without any meaningful improvement in output quality.

#### 2. Why does the security auditor NOT have Write in its tools list?

The security auditor is a read-only reviewer — its job is to identify and report issues, not fix them. Giving it Write access would be dangerous because a security agent that can also modify files could accidentally alter the very infrastructure it is auditing. Keeping it read-only enforces the principle of least privilege — the same principle it checks for in your Terraform code.

#### 3. Why does the tf-writer use `inherit` instead of a specific model?

The tf-writer uses inherit because it should use whatever model the main Claude Code session is already running. Since writing production Terraform code is a complex, high-stakes task, it makes sense to inherit the most capable model available in the current session rather than locking it to a specific one. This keeps it flexible — if you upgrade your session model, the tf-writer automatically benefits.

### Evidence

#### Screenshot 2 — `security-auditor.md` frontmatter showing model and tools configuration

[security-auditor-frontmatter](screenshots/security-auditor.md-frontmatter.png)

#### Screenshot 3 — `cost-optimizer.md` frontmatter showing the model and tools configuration

[cost-optimizer-frontmatter](screenshots/cost-optimizer.md-frontmatter.png)



# Task 3 — Run the Security Auditor

## Goal

Trigger the security auditor agent and analyze the generated security report for your Terraform infrastructure:

**What I did**

I triggered the security-auditor using a natural language prompt instead of selecting the agent manually. Claude automatically identified the correct specialist, delegated the task, and generated a detailed security report organized by severity levels. This demonstrated how subagents can independently handle specialized responsibilities within an agentic workflow.

### Evidence

#### Screenshot 4 — The delegation message showing Claude launched the security-auditor

[security auditor](screenshots/Delegation-message-showing-Claude-launched-the-security-auditor.png)

#### Screenshot 5 — Security audit report output

[Full Security Audit Report](screenshots/Full-security-audit-report-with-findings-visible.png)



# Task 4 — Run the Cost Optimizer

## Goal

Trigger the cost optimizer agent and review the generated cost optimization report:

**What I did**

I asked Claude to review my Terraform infrastructure for cost optimization. The request was automatically delegated to the cost-optimizer subagent, which quickly analyzed the deployment and provided recommendations to reduce infrastructure costs while maintaining performance. This highlighted how different specialized agents can focus on specific areas such as cost management.

### Evidence

#### Screenshot 6 — The full cost optimization report

[Full cost Report](screenshots/The%20-full-cost-optimization-report.png)


# Submission Instructions

- Ensure all agent files are committed in `.claude/agents/`
- Complete all written answers in your GitHub Repo
- Push final changes to your forked GitHub repository

---

## GitHub Repository URL

Paste your forked repository URL here:

[Forked Repo URL](https://github.com/kakpoklebervincent/Ultimate-Agentic-DevOps-with-Claude-Code)

# Completion Checklist

- [ ] `.claude/agents/` folder contains all 3 agent files
- [ ] Screenshot 2 shows correct `security-auditor.md` configuration
- [ ] Screenshot 3 shows correct `cost-optimizer.md` configuration
- [ ] All 3 written answers completed 
- [ ] Security auditor executed successfully
- [ ] Cost optimizer executed successfully
- [ ] Security report is visible with findings
- [ ] Cost report is visible with recommendations
- [ ] All required screenshots added
- [ ] GitHub repo updated with agents

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