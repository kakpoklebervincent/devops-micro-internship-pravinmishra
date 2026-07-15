# Assignment 2 — Deploy a React App on Ubuntu VM Using Nginx

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will deploy a React application on an Ubuntu EC2 instance and serve it using Nginx. You will provision a Linux server, install the required tools, personalize the application with your details, and verify that it is publicly accessible via a browser.

---

# Task 1 — Setup Environment (Node.js & npm)

## Goal

Install Node.js and npm on the Ubuntu VM and verify the installation:

### Implementation Summary

The Ubuntu package repositories were updated to ensure that the latest package information was available before installing new software. Node.js and npm were then installed to provide the JavaScript runtime and package manager required to build and manage the React application. The installation was verified by checking the installed versions, confirming that the development environment was successfully prepared for the deployment process.

### Evidence

#### Screenshot 1 — Output of `node -v && npm -v` showing installed versions

[Output node && npm](screenshots/01-node-npm-version.png)

---

# Task 2 — Setup Environment (Nginx)

## Goal

Install Nginx, start the service, and confirm it is running:

### Implementation Summary

Nginx was installed to serve the React application's static files over HTTP. The installation was verified by checking the installed version before starting the service. The web server was then configured to start automatically during system boot, and its operational status was confirmed to ensure it was ready to host the application.


### Evidence

#### Screenshot 2 — Output of `systemctl status nginx --no-pager` showing Active (running)

[systemctl status nginx output](screenshots/02-nginx-status.png)

---

# Task 3 — Clone React Application

## Goal

Clone the project repository and verify the project files are present:

### Implementation Summary

The React application repository was cloned from GitHub to the Ubuntu EC2 instance, making the project source code available for deployment. After navigating to the project directory, the repository contents were listed to confirm that all required files and folders had been downloaded successfully. This verification ensured that the application was ready for customization and the production build process.


### Evidence

#### Screenshot 3 — Output of `ls` inside the `my-react-app` directory showing project files

[my-react-app files](screenshots/03-react-project-files.png)

---

# Task 4 — Modify Application (Personalization)

## Goal

Update `App.js` with your full name and the current date:

### Implementation Summary

The React application's main component was personalized by replacing the default placeholder values with the deployer's details. The full name and deployment date were updated to uniquely identify the current implementation while meeting the assignment requirements. These changes will be reflected in the deployed application, providing clear confirmation that the application was successfully customized before deployment.

### Evidence

#### Screenshot 4 — `nano App.js` open showing your full name and date filled in

[Editted App.js showing my name](screenshots/04-appjs-personalization.png)

---

# Task 5 — Build React Application

## Goal

Install dependencies and generate the production build:

### Implementation Summary

Project dependencies were installed using npm to ensure that all required libraries and packages were available for the application. An optimized production build was then generated, transforming the React source code into static files suitable for deployment. The presence of the `build` directory confirmed that the production build was generated successfully and that the application was ready to be deployed to the Nginx web server.

### Evidence

#### Screenshot 5 — Output of `ls` inside `my-react-app` showing the `build/` folder generated

[Output Build in my-react-app](screenshots/05-react-build-folder.png)

---

# Task 6 — Deploy React Build to Nginx Web Root

## Goal

Copy the production build files to the Nginx web root directory.

### Implementation Summary

The optimized React build was deployed by copying the generated application files into Nginx's default web root directory (`/var/www/html`). File ownership and permissions were then configured to ensure that the Nginx service could securely access and serve the application. The deployment was verified by listing the contents of the web root, confirming that all production files had been copied successfully.

### Evidence

#### Screenshot 6 — Output of `ls /var/www/html/` showing the deployed build contents

[Output of `ls /var/www/html/](screenshots/06-nginx-webroot.png)

---

# Task 7 — Configure Nginx for React Application

## Goal

Apply Nginx configuration for React routing and confirm the service is active:

### Implementation Summary

The default Nginx configuration was updated to correctly serve the React Single Page Application (SPA) by redirecting unmatched routes to `index.html`. The configuration was validated to ensure there were no syntax errors before restarting the web server. Finally, the Nginx service status and configuration file were verified, confirming that the server was correctly configured to host the deployed application.


### Evidence

#### Screenshot 7 — Output of `systemctl is-active nginx` showing `active`

[systemctl is-active nginx](screenshots/07-nginx-active.png)

---

#### Screenshot 8 — Output of `cat /etc/nginx/sites-available/default` showing the Nginx config

[Screenshot 8](screenshots/08-nginx-configuration.png)

---

# Task 8 — Test Deployment

## Goal

Verify the React application is publicly accessible via the server's public IP.

### Implementation Summary

The server's public IP address was retrieved to confirm the network endpoint for the deployed application. The React application was then accessed through a web browser using the public IP, verifying that Nginx was successfully serving the production build. The displayed application, together with the personalized deployment details, confirmed that the deployment was completed successfully and was accessible over the internet.


### Evidence

#### Screenshot 9 — Output of `curl ifconfig.me` showing the server's public IP address

[Output of `curl ifconfig.me](screenshots/09-public-ip.png)

---

#### Screenshot 10 — Browser showing the deployed React app at `http://<public-ip>` with your name and date visible

[Browser showing the deployed React app](screenshots/10-react-app-browser.png)
---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`__________________________`

---

#### Screenshot — LinkedIn post showing the deployed application

Add your screenshot here.

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Node.js and npm installed and verified (Screenshot 1)
- [ ] Nginx installed and running (Screenshot 2)
- [ ] Repository cloned and files verified (Screenshot 3)
- [ ] App.js updated with full name and date (Screenshot 4)
- [ ] Production build generated (Screenshot 5)
- [ ] Build files deployed to Nginx web root (Screenshot 6)
- [ ] Nginx configured and active (Screenshots 7 & 8)
- [ ] Public IP retrieved (Screenshot 9)
- [ ] React app accessible in browser with personal details visible (Screenshot 10)
- [ ] LinkedIn post published and URL submitted
- [ ] No sensitive data exposed

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