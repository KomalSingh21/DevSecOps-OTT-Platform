# Prerequisites

## AWS
- AWS account with EC2 and Amazon EKS access.
- Ubuntu 24.04 LTS EC2 instance.
- Recommended: `2 vCPUs, 4 GiB memory`
- 20 GB gp3 storage.
- Existing or new SSH key pair.
- Security group access for `22`, `80`, `443`, `8080` (Jenkins), `8081` (OTT), `9000` (SonarQube), `9090` (Prometheus), `3000` (Grafana), and the EKS NodePort used by the application.

## Accounts / Credentials
- GitHub repository for the OTT application.
- TMDB account and API key.
- Docker Hub account.
- GitHub repository containing the Kubernetes manifests used by ArgoCD.

Store secrets in Jenkins Credentials:
- `tmdb-api-key`
- `sonar-token`
- `docker`
- Jenkins SMTP/App Password where email notifications are configured.

## Tools
Git, Docker, Java, Jenkins, Node.js, SonarQube, SonarQube Scanner,
OWASP Dependency-Check, Trivy, Docker Hub, Prometheus, Node Exporter,
Grafana, kubectl, Helm, Amazon EKS and ArgoCD.
