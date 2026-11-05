# 🛒 E-Commerce Microservices Automation Platform (DevOps Case Study IA-1)

![Ansible](https://img.shields.io/badge/Ansible-2.10%2B-CC0000?style=for-the-badge&logo=ansible&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-24.0%2B-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-1.24%2B-009639?style=for-the-badge&logo=nginx&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14.0%2B-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-2.53%2B-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-11.1%2B-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%2F24.04_WSL2-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

An automated Infrastructure as Code (IaC) deployment for a multi-tiered, microservices-based e-commerce platform using **Ansible Engine** on Ubuntu (WSL2). This repository contains playbooks, inventories, Jinja2 templates, and group variables designed for zero-downtime provisioning, service configuration, relational schema setup, and full-stack telemetry monitoring.

---

## 📌 Project Information

- **Student Name**: Yash Dhananjay Dalavi
- **PRN**: 202301100017
- **Roll Number**: CCF54
- **Branch**: Computer Engineering – Software Engineering
- **Course**: Cloud Native DevOps
- **Assignment**: DevOps Case Study & Ansible Automation (IA-1)

---

## 🏗️ Application & System Architecture

```
                               ┌─────────────────────────────────────────┐
                               │       Ansible Control Node (WSL2)       │
                               └────────────────────┬────────────────────┘
                                                    │
                                   ansible-playbook -i inventory.ini
                                                    │
       ┌────────────────────────────────────────────┼────────────────────────────────────────────┐
       │                                            │                                            │
       ▼                                            ▼                                            ▼
┌──────────────┐                             ┌──────────────┐                             ┌──────────────┐
│  webservers  │                             │  dbservers   │                             │  monitoring  │
├──────────────┴──────────────┐              ├──────────────┴──────────────┐              ├──────────────┴──────────────┐
│ • Nginx Reverse Proxy (:8080)│              │ • PostgreSQL RDBMS (:5432)   │              │ • Node Exporter agent (:9100)│
│ • User Service Docker Container (:8001)   │ • Database: ecommerce_db     │              │ • Prometheus Engine (:9090) │
│ • Order Service Docker Container (:8002)  │ • Schema: users, orders     │              │ • Grafana Dashboard (:3000) │
└─────────────────────────────┘              └─────────────────────────────┘              └─────────────────────────────┘
```

---

## 📦 Component Breakdown

| Tier | Component | Functionality | Deployment Type | Port |
| :--- | :--- | :--- | :--- | :--- |
| **Frontend/Proxy** | **Nginx** | Reverse proxy, static storefront routing, SSL/header termination | Native Service | `:8080` |
| **Microservice** | **User Service** | Microservice endpoint handling user registration & profiles | Docker Container | `:8001` |
| **Microservice** | **Order Service** | Microservice endpoint tracking transaction workflow | Docker Container | `:8002` |
| **Database** | **PostgreSQL** | Relational data persistence layer for users and orders schema | Native Service | `:5432` |
| **Monitoring** | **Node Exporter** | Host machine hardware & kernel telemetry collector | Docker Container | `:9100` |
| **Monitoring** | **Prometheus** | Time-series metrics scraper engine | Docker Container | `:9090` |
| **Analytics** | **Grafana** | Real-time metric visualization dashboard UI | Docker Container | `:3000` |

---

## 🔄 DevOps Lifecycle & Automation Strategy

```
[ Plan: Jira/GitHub ] ──► [ Code: Git ] ──► [ Build: Docker ] ──► [ Test: Syntax Verification ]
                                                                             │
                                                                             ▼
[ Monitor: Prometheus/Grafana ] ◄── [ Operate: systemd ] ◄── [ Deploy: Ansible Engine ]
```

1. **Continuous Configuration & Deployment**: Ansible playbooks execute declaratively with strict idempotency (subsequent runs produce `changed=0`).
2. **Infrastructure as Code (IaC)**: Environment settings, database access control, dynamic proxy routes, and scraper targets are version-controlled using modular Jinja2 templates (`.j2`) and `group_vars`.
3. **Continuous Monitoring & Feedback**: Telemetry scraped by Prometheus is dynamically provisioned as a datasource in Grafana for real-time tracking of CPU and RAM load.

---

## 📂 Repository Directory Layout

```text
ecommerce-devops-ansible/
├── ansible.cfg                    # Ansible engine runtime configuration
├── inventory.ini                  # Multi-tiered inventory hosts file
├── deploy_ecommerce.yml           # Main site execution playbook (4 plays)
├── group_vars/                    # Environment variables hierarchy
│   ├── all/
│   │   └── vars.yml               # Global variables & application settings
│   ├── dbservers.yml              # Database connection & tuning parameters
│   ├── webservers.yml             # Web tier & container port bindings
│   └── monitoring.yml             # Prometheus & Grafana configuration values
└── templates/                     # Dynamic Jinja2 configuration templates
    ├── index.html.j2              # Custom e-commerce landing page
    ├── nginx.conf.j2              # Nginx reverse proxy configuration template
    ├── postgres_custom.conf.j2    # PostgreSQL performance tuning config
    ├── prometheus.yml.j2          # Prometheus dynamic scrape targets
    └── grafana_datasource.yml.j2  # Grafana automated datasource setup
```

---

## 🚀 Step-by-Step Installation & Execution Guide

### Prerequisites
- Operating System: **Ubuntu 22.04 LTS / 24.04 LTS (WSL2 or Bare-Metal)**
- Packages: `ansible`, `git`, `python3`, `docker.io`

### 1. Clone the Repository
```bash
git clone https://github.com/yash-D-Dalavi/ecommerce-devops-ansible.git
cd ecommerce-devops-ansible
```

### 2. Verify Connectivity & Host Inventory
```bash
# Graph inventory structure
ansible-inventory -i inventory.ini --graph

# Test connection across all host tiers
ansible all -m ping -i inventory.ini
```

### 3. Validate Playbook Syntax
```bash
ansible-playbook -i inventory.ini deploy_ecommerce.yml --syntax-check
```

### 4. Execute Complete Automation Playbook
```bash
ansible-playbook -i inventory.ini deploy_ecommerce.yml -K
```

### 5. Verify Idempotency (Re-execution)
Run the playbook again to confirm zero configuration drift:
```bash
ansible-playbook -i inventory.ini deploy_ecommerce.yml -K
```
*Expected recap output: `changed=0` across all healthy host nodes.*

---

## 🧪 Verification Commands & Live Endpoints

### Live Application URLs
- **Storefront Landing Page**: [http://localhost:8080](http://localhost:8080)
- **User Microservice API**: [http://localhost:8080/api/users/](http://localhost:8080/api/users/)
- **Order Microservice API**: [http://localhost:8080/api/orders/](http://localhost:8080/api/orders/)
- **Prometheus Scraper Engine**: [http://localhost:9090](http://localhost:9090)
- **Grafana Metrics Dashboard**: [http://localhost:3000](http://localhost:3000) *(User: `admin` | Password: `GrafanaAdmin2026`)*

### Terminal Health Check Commands
```bash
# Check status of running containers
sudo docker ps

# Check status of native host systemd services
sudo systemctl status nginx postgresql --no-pager

# Verify created database schema & tables
sudo -u postgres psql -d ecommerce_db -c '\dt'
```

---

## 🔒 Security Practices (DevSecOps Integration)

- **Secrets Isolation**: Sensitive credentials (database passwords, admin keys) are isolated inside `group_vars` and managed securely via Ansible Vault patterns.
- **Least-Privilege Database User**: Database accounts are created with explicit `NOSUPERUSER` flags and authenticated using `scram-sha-256`.
- **Non-Root Container Runtimes**: Docker containers execute under restricted operational contexts with resource logging limits enforced by the Ansible engine.

---

## 📜 License

This project is open-source and available under the [MIT License](LICENSE).
