CI/CD DEPLOYMENT ASSIGNMENT
Flask Backend & Express Frontend on Amazon EC2
Automated CI/CD using Jenkins and GitHub Webhooks
 
Submitted By
Dilip Chaluvadi
Cloud Platform
Amazon Web Services (AWS)
Compute Service
Amazon EC2 — Ubuntu
CI/CD Tool
Jenkins
Source Control
GitHub

 
Project Status: Successfully Deployed and Tested

 Flask Backend Repository:  https://github.com/devopsdilip/flask-backend-cicd
Express Frontend Repository:  https://github.com/devopsdilip/express-frontend-cicd
 
1. Project Overview
This project demonstrates the deployment of a Flask backend and an Express.js frontend on a single Amazon EC2 instance. Jenkins is used to implement continuous integration and continuous deployment (CI/CD), while GitHub repositories act as the source-control system. GitHub webhooks are configured so that a push to the application repository can automatically trigger the corresponding Jenkins pipeline.
Project Objectives
·       Provision an Ubuntu-based Amazon EC2 instance.
·       Install Python, Node.js, Git, PM2, Java and Jenkins.
·       Deploy the Flask backend on port 5000.
·       Deploy the Express frontend on port 3000.
·       Use PM2/systemd to keep applications running.
·       Create separate Jenkins pipelines for Flask and Express.
·       Automate dependency installation, testing, deployment and health checks.
·       Use GitHub webhooks to trigger Jenkins after repository pushes.
·       Collect screenshots as evidence of successful deployment and CI/CD execution.
·        
2. System Architecture
Both applications and Jenkins are hosted on the same EC2 instance. The Flask service listens on port 5000, the Express service listens on port 3000, and Jenkins is available on port 8080. PM2 manages the Express process, while systemd manages the Flask service.
Layer
Component
Purpose
Source Control
GitHub
Stores Flask and Express source code
CI/CD
Jenkins
Build, test, deploy and health-check automation
Compute
AWS EC2 Ubuntu
Hosts applications and Jenkins
Backend
Flask :5000
Provides backend/API service
Frontend
Express :3000
Provides web interface
Process Management
systemd + PM2
Keeps services running

GitHub
    │
    ├── Flask Repository ──► Jenkins Flask Pipeline ──► EC2 ──► Flask :5000
    │
    └── Express Repository ─► Jenkins Express Pipeline ─► EC2 ──► Express :3000
                                   	│
                                   	└── Health Checks

 GitHub Push ──► Webhook ──► Jenkins ──► Checkout ─► Install ─► Test ─► Deploy ─► Verify
3. AWS EC2 Infrastructure
A single Ubuntu EC2 instance was used for the assignment. The server was prepared with the runtime dependencies required by both applications and Jenkins.
Component
Version / Port
Role
Ubuntu
24.04 / AWS EC2
Operating system
Python
3.12.3
Flask runtime
Node.js
20.20.2
Express runtime
Npm
10.8.2
Node package management
PM2
7.0.3
Express process manager
Java
21.0.11
Jenkins runtime
Jenkins
Port 8080
CI/CD automation
Flask
Port 5000
Backend service
Express
Port 3000
Frontend service

Application Directories
/opt/apps/
 ├── flask-backend/
 └── express-frontend/
Verification Commands
python3 --version
 node --version
 npm --version
 pm2 --version
 java -version
 sudo systemctl status jenkins





 Paste screenshot here

Evidence: EC2 instance running in AWS Console.
4. Flask Backend Deployment
The Flask repository was cloned into /opt/apps/flask-backend. A Python virtual environment was created and the dependencies listed in requirements.txt were installed.
cd /opt/apps/flask-backend
 python3 -m venv venv
 source venv/bin/activate
 pip install -r requirements.txt
The Flask application was configured to listen on port 5000. A systemd service was used so that Jenkins can restart the backend after deployment and the application can remain active.
sudo systemctl status flask-backend
 sudo systemctl restart flask-backend
 curl http://localhost:5000/health
The health endpoint returned a successful response:
{"health":"OK"}





 Paste screenshot here

Evidence: Browser or terminal showing the Flask service responding on port 5000.
5. Express Frontend Deployment
The Express repository was deployed to /opt/apps/express-frontend. Node.js dependencies were installed using npm and PM2 was used to manage the Express process.
cd /opt/apps/express-frontend
 npm install
 pm2 start server.js --name express-frontend
 pm2 save
 pm2 status
The Express application was configured to listen on port 3000.
curl http://localhost:3000
 pm2 status
The Express health validation returned the Student Registration HTML page, confirming that the frontend was reachable and the process was online.





 Paste screenshot here

Evidence: Browser showing the Student Registration page at port 3000.
6. Jenkins CI/CD Configuration
Jenkins was installed on the same EC2 instance and runs as a systemd service. GitHub SSH authentication was configured for Jenkins so it could securely access the private repositories.
sudo systemctl status jenkins
 sudo -u jenkins ssh -T git@github.com
The GitHub SSH test successfully authenticated the Jenkins user with GitHub.
6.1 Flask Jenkins Pipeline
The Flask pipeline contains the following stages:
·       Checkout — retrieves the latest source code from GitHub.
·       Install Dependencies — runs pip install using the application's virtual environment.
·       Test — runs Python compilation validation with py_compile.
·       Deploy — copies the application files, installs dependencies and restarts flask-backend.
·       Health Check — waits briefly and verifies http://localhost:5000/health.
6.2 Flask Pipeline Evidence
The successful Flask build completed all stages. The final health check returned {"health":"OK"} and Jenkins reported
"Flask deployment successful!"

6.3 Express Jenkins Pipeline
The Express pipeline contains the following stages:
·       Checkout — retrieves the latest source code from GitHub.
·       Install Dependencies — runs npm install.
·       Test — validates server.js using node --check.
·       Deploy — updates application files, runs npm install and restarts Express through PM2.
·       Health Check — verifies http://localhost:3000.
6.4 Express Pipeline Evidence
The Express Jenkins build completed successfully. PM2 reported the express-frontend process as online and the health check returned the Student Registration HTML page.

 
 
7. GitHub Webhook Configuration
GitHub webhooks provide the event-driven trigger for CI/CD. When code is pushed to the main branch, GitHub sends an HTTP request to Jenkins. Jenkins then starts the corresponding pipeline.
Webhook Test Procedure
1.       Make a small, harmless change to the repository.
2.       Commit the change and push it to the main branch.
3.       Open Jenkins and verify that a new build starts automatically.
4.       Open the GitHub repository Settings → Webhooks and inspect Recent Deliveries.
5.       Confirm that the webhook delivery is successful and Jenkins executes the pipeline.
git add README.md
 git commit -m "Test Jenkins webhook"
 git push origin main


 
8. Network and Security Group Configuration
The EC2 security group must permit SSH access for administration and HTTP/TCP access to the application and Jenkins ports used during the assignment. For a production deployment, access should be restricted to trusted IP addresses and HTTPS should be placed in front of public services.
Port
Protocol
Purpose
Recommended Source
22
TCP
SSH administration
My IP only
3000
TCP
Express frontend
My IP / required users
5000
TCP
Flask backend testing
My IP / required users
8080
TCP
Jenkins UI / webhook
My IP; webhook access as required


9. Process Management Verification
The final runtime state can be verified using the following commands:
pm2 status
 sudo systemctl status flask-backend
 sudo systemctl status jenkins
Expected state: express-frontend is online under PM2, flask-backend is active under systemd, and Jenkins is active and listening on port 8080.


 
10. End-to-End Testing
Test
Command / URL
Expected Result
Status
Flask health
http://EC2-PUBLIC-IP:5000/health
{"health":"OK"}
PASS
Express frontend
http://EC2-PUBLIC-IP:3000
Student Registration page
PASS
Flask Jenkins
Jenkins → Flask pipeline
SUCCESS
PASS
Express Jenkins
Jenkins → Express pipeline
SUCCESS
PASS
Webhook
GitHub push → Jenkins
Automatic build
PASS / VERIFY

11. Troubleshooting and Resolution
Issue
Resolution
GitHub HTTPS authentication failed
GitHub no longer accepts account passwords for Git operations. SSH authentication was configured and verified for Jenkins.
Flask Jenkins permission denied
The application directory ownership/permissions were corrected and the Jenkins deployment was configured with the required sudo permission to restart the service.
Flask deployment health check
The pipeline was updated to deploy files, restart flask-backend and validate /health. The final response was {"health":"OK"}.
Express deployment
The pipeline runs npm install, updates server files, restarts the PM2 process and validates port 3000.

12. Source Code Repositories
Flask Backend Repository:
https://github.com/devopsdilip/flask-backend-cicd
Express Frontend Repository:
https://github.com/devopsdilip/express-frontend-cicd
14. Conclusion
The assignment successfully demonstrates a complete CI/CD workflow for a Flask backend and Express frontend hosted on a single AWS EC2 instance. GitHub provides source control, Jenkins automates checkout, dependency installation, testing, deployment and health validation, and PM2/systemd keep the applications running. The successful Jenkins builds and application health checks provide evidence that the deployment process is repeatable and automated.
