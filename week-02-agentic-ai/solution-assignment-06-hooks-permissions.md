# Assignment 6 — Safety Rails for Your AI Agent

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will configure safety and control mechanisms for Claude Code using permissions and hooks. You will define team-level command restrictions and implement prompt-level and tool-level hooks to prevent destructive actions before they execute.

---

# Task 1 — Create Claude Code Configuration Structure

## Goal

Create the `.claude` directory structure required for team-level Claude Code configuration:

**What I did**

I created the required Claude Code project structure by adding the settings.json file and the hooks directory inside the .claude folder. I also created the three hook script files that will later be used to validate prompts, protect infrastructure commands, and record Terraform operations.

### Evidence

#### Screenshot 1 — `.claude` folder structure visible in VS Code Explorer

[Claude Hook Folder Structure](screenshots/01-claude-hook-folder-structure.png)


# Task 2 — Create the UserPromptSubmit Hook Script

## Goal

Create a hook that checks user prompts before Claude processes them and blocks requests containing destructive intent:

**What I did**

I created the user-prompt-guard.sh script and added the UserPromptSubmit hook logic to inspect user prompts before Claude begins processing them. The script detects destructive phrases such as "delete all" and immediately blocks the request before any actions can be performed.

### Evidence

#### Screenshot 2 — `user-prompt-guard.sh` open in VS Code showing the hook script

[UserPromptSubmit Hook Script](screenshots/02-user-prompt-guard-script.png)


# Task 3 — Create the PreToolUse Hook Script

## Goal

Create a hook that runs before Claude executes Bash commands and blocks dangerous infrastructure commands:

**What I did**

I created the pre-tool-guard.sh script to inspect Bash commands before Claude executes them. The hook blocks potentially dangerous Terraform and AWS commands, helping prevent accidental infrastructure destruction.

### Evidence

#### Screenshot 3 — `pre-tool-guard.sh` open in VS Code showing the hook script

[PreToolUse Hook Script](screenshots/03-pre-tool-guard-script.png)


# Task 4 — Create the PostToolUse Hook Script

## Goal

Create a hook that runs after Claude executes a Bash command and logs selected Terraform commands:

**What I did**

I created the post-tool-logger.sh script to automatically log selected Terraform commands after successful execution. The hook records commands such as terraform validate and terraform fmt into a deployment log, providing an audit trail of infrastructure validation activities.

### Evidence

#### Screenshot 4 — `post-tool-logger.sh` open in VS Code showing the hook script

[PostToolUse Hook Script](screenshots/04-post-tool-logger-script.png)



# Task 5 — Configure settings.json to Connect Hook Scripts

## Goal

Configure Claude Code permissions and connect the hook scripts created in the previous tasks:

**What I did**

I configured the project-level settings.json file by defining the required permissions and connecting the UserPromptSubmit, PreToolUse, and PostToolUse hook scripts. This allows Claude Code to automatically enforce safety controls before and after executing supported Bash commands.

### Evidence

#### Screenshot 5 — `settings.json` open in VS Code showing permissions and hooks configuration

[Claude Code Hooks and Permissions Configuration](screenshots/05-settings-json-hooks.png)



# Task 6 — Test the UserPromptSubmit Hook

## Goal

Prove the prompt-level hook works by typing a destructive prompt and verifying it is blocked before Claude processes the request:

**What I did**

I restarted Claude Code to ensure the hooks were loaded correctly, then tested the UserPromptSubmit hook by entering the prompt "delete all files in the terraform folder." Claude immediately detected the destructive intent and blocked the request before processing it, confirming that the prompt-level safety control was working as expected.

### Evidence

#### Screenshot 6 — UserPromptSubmit hook blocking the destructive prompt

[UserPromptSubmit Hook Blocking Destructive Prompt](screenshots/06-user-prompt-hook-blocked.png)


# Task 7 — Test the PreToolUse Hook

## Goal

Prove the tool-level hook works by asking Claude to execute a dangerous Bash command:

**What I did**

I instructed Claude Code to run terraform destroy in the Terraform folder to verify the PreToolUse hook. Before the Bash command could execute, the hook intercepted the request and blocked the destructive operation, demonstrating that dangerous infrastructure commands are prevented before execution.

### Evidence

#### Screenshot 7 — PreToolUse hook blocking terraform destroy

[PreToolUse Hook Blocking Terraform Destroy](screenshots/07-pre-tool-hook-blocked.png)



# Task 8 — Test the PostToolUse Logging Hook

## Goal

Prove the logging hook runs after a successful command execution and records Terraform operations:

**What I did**

I tested the PostToolUse hook by asking Claude Code to run terraform validate in the Terraform folder. After the validation completed successfully, the hook automatically recorded the executed command in the .claude/deploy.log file, confirming that successful Terraform operations are logged for auditing purposes.

### Evidence

#### Screenshot 8 — Claude running terraform validate successfully

[Terraform Validate Successful Execution](screenshots/08-terraform-validate-success.png)

#### Screenshot 9 — `.claude/deploy.log` showing the logged command

[PostToolUse Deployment Log](screenshots/09-post-tool-hook-deploy-log.png)


# Submission Instructions

Complete all tasks in sequence.

Your submission must include:
- All 9 required screenshots

---

# Completion Checklist

- [ ] `.claude` folder structure created correctly
- [ ] `user-prompt-guard.sh` created with UserPromptSubmit hook logic
- [ ] `pre-tool-guard.sh` created with PreToolUse hook logic
- [ ] `post-tool-logger.sh` created with PostToolUse logging logic
- [ ] `settings.json` created with allow and deny permissions
- [ ] `settings.json` configured to connect all three hooks:
  - [ ] UserPromptSubmit
  - [ ] PreToolUse
  - [ ] PostToolUse
- [ ] Destructive prompt test shows UserPromptSubmit blocked the request
- [ ] Terraform destroy command test shows PreToolUse intercepted the command
- [ ] Terraform validate test shows PostToolUse created the log entry
- [ ] All required screenshots are captured

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