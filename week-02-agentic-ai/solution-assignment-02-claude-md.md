clear# Assignment 2 — Teaching Claude Your Project

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will create and customize a `CLAUDE.md` file for your project using `/init`. You will then modify it with project-specific rules and verify how it changes Claude’s behavior through before-and-after testing.

---

# Task 1 — Capture the Before State

## Goal

Capture Claude’s response before `CLAUDE.md` exists in the project to establish a baseline behavior:

The downloaded repository already contained a CLAUDE.md file (This was visible is the assignment-01), so I removed it to comply with the assignment requirement. After confirming the project no longer had a CLAUDE.md file, I asked Claude Code to describe the project and deployment approach. As expected, the response was generic because no project specific guidance had been provided.

### Evidence

#### Screenshot 1 — Claude response before CLAUDE.md
[Claude response before CLAUDE](screenshots/Claude-Response-Without-Cloud-Md.png)
#### Screenshot 1 — Claude’s generic response before CLAUDE.md exists (project contains only `index.html`, `style.css`, `images/`, `README.MD`, `privacy.html`, `terms.html`)

Add your screenshot here.


# Task 2 — Generate the First Draft with /init

## Goal

Generate an initial `CLAUDE.md` file using the `/init` command and review the auto-generated content:

I used the /init command to generate a starter CLAUDE.md file and reviewed the automatically generated project instructions in Visual Studio Code.

### Evidence

#### Screenshot 2 — The auto-generated CLAUDE.md open in VS Code showing its content

[Generated Claude.md](screenshots/Claude.md-Generated-in-VSCode.png)


# Task 3 — Customize the CLAUDE.md

## Goal

Update the generated `CLAUDE.md` file by adding project-specific instructions across all required sections:

I customized the generated CLAUDE.md by updating the project overview, architecture, commands, conventions, and safety sections with project specific guidance so Claude Code could better understand the repository and follow the required development practices.

### Evidence

#### Screenshot 3 — Your customized CLAUDE.md in VS Code showing all 5 sections (scroll to show the full file)

[Customized Claude.md](screenshots/customized%20CLAUDE.md%20in%20VS.PNG)


# Task 4 — Test the After State

## Goal

Verify that Claude’s behavior changes after adding `CLAUDE.md` by running a new session and comparing responses before and after context is applied:

I started a new Claude Code session to verify that the updated CLAUDE.md was being applied. Claude correctly identified the project's AWS deployment architecture and refused to add a React component because it would violate the project's "No JavaScript" convention.

### Evidence

#### Screenshot 4 — Claude's specific, detailed answer after reading CLAUDE.md (Claude mentioning S3, CloudFront and Terraform)

[Claude Respond to project spec](screenshots/CLAUDE.md-Answering-Question.png)


#### Screenshot 5 — Claude response rejecting React/component change based on rules

[Claude Rejecting prompt](screenshots/Claude-refusing-against-adding-React.png)



# Submission Instructions

- Ensure `CLAUDE.md` is committed to your GitHub repository
- Add all required screenshots to your submission
- Push your final changes to your forked repository

---

## GitHub Repository URL

Paste your forked repository URL here:

[Forked Repo URL](https://github.com/kakpoklebervincent/Ultimate-Agentic-DevOps-with-Claude-Code)




# Completion Checklist

[ ] Screenshot 1 shows a generic Claude response (no CLAUDE.md)<br>
[ ] Screenshot 2 shows the auto-generated `/init` output <br>
[ ] Screenshot 3 shows all 5 sections in your customized CLAUDE.md <br>
[ ] Screenshot 4 shows Claude mentioning S3, CloudFront, and Terraform <br>
[ ] Screenshot 5 shows Claude refusing the React request <br>
[ ] Screenshot 6 shows `CLAUDE.md` committed and visible in your GitHub repository <br>
[ ] GitHub repository URL is included in the submission <br>

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