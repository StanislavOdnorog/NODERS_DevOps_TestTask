# Test Assignment: Ansible, Docker Compose, Linux Security

## 📌 Project Description
This repository contains infrastructure for deploying and configuring a server on **Ubuntu Server** using **Ansible, Docker Compose**, and **GitHub Actions (CI/CD)**. It includes:

## 📂 Repository Structure

```
├── .github/workflows/      # CI/CD Workflows for automatic deployment
│   ├── deploy.yaml         # Infrastructure deployment via GitHub Actions
│   ├── lint.yaml           # Code linting (YAML, Ansible lint)
│
├── ansible/                # Main directory with Ansible roles
│   ├── inventory/          # Inventory files
│   │   ├── hosts.yaml      # Server descriptions (monitoring, security, web)
│   │   ├── group_vars/     # Variables for host groups
│   │   ├── host_vars/      # Variables for individual hosts
│   ├── roles/              # Ansible roles
│   │   ├── users/          # Role for user creation
│   │   ├── security/       # Role for security setup (SSH, Firewall, Fail2Ban)
│   │   ├── nginx/          # Role for installing and configuring Nginx
│   │   ├── docker/         # Role for installing Docker
│   │   ├── monitoring/     # Deployment of Prometheus and Grafana with Docker Compose
│
├── monitoring/             # Monitoring configuration (Prometheus, Grafana)
│   ├── docker-compose.yaml # Main file for launching monitoring
│   ├── grafana/            # Grafana configuration
│   │   ├── dashboards/     # JSON files with Grafana dashboards
│   │   ├── provisioning/   # Grafana auto-configuration
│   ├── prometheus/         # Prometheus configuration
│
├── site.yaml               # Main playbook for running basic roles (users, security)
├── monitoring.yaml         # Playbook for monitoring (nginx, docker, monitoring)
├── ansible.cfg           
├── .gitignore              
└── README.md              
```


## 🚀 Project Deployment

### **1. Configure Variables**

Edit the `inventory/group_vars/all.yaml` files or use **environment variables**.

Edit the `inventory/host_vars/` files for individual hosts if needed.

```yaml
Main variables in group_vars/all.yaml that control deployment:

    users:  # List of users to be created
      - name:  # Username
        ssh_key:  # SSH public key
        groups:  # Assigned groups
        shell:  # Default shell
        state:  # User state (present/absent)
        create_home:  # Whether to create a home directory

    security_ssh_port:  # SSH port
    security_allowed_ports:  # Allowed firewall ports
      - ...

    nginx_upstreams:  # Nginx upstream configuration
     - name:  # Upstream name
       servers:  # Backend servers
         - ...
       keepalive:  # Keepalive connections
       strategy:  # Load balancing strategy
       keepalive:  # Keepalive setting

    nginx_servers:  # Nginx server configuration
     - port:  # Server port
       server_name:  # Domain name
       ssl:  # SSL enabled (true/false)
       ssl_cert:  # SSL certificate path
       ssl_key:  # SSL key path
       locations:  # Location-based routing
         - path:  # URL path
           upstream:  # Upstream backend
           extra_config: |  # Additional Nginx configuration
             ...

Main variables that can be added as secrets in GitHub Actions:
    - GRAFANA_ADMIN_PASSWORD  # Admin password for Grafana
    - GRAFANA_ADMIN_USER  # Admin username for Grafana
    - SSH_PRIVATE_KEY  # SSH private key for authentication
    - USER_PASSWORDS  # Encrypted user passwords
```

### **2. Modify Monitoring Configuration**

1. **Create a new dashboard in Grafana** and export it as JSON (into `roles/monitoring/files/grafana/dashboards/`)
2. **Add new services to Prometheus** (inside `roles/monitoring/files/prometheus/prometheus.yaml`)
3. **Modify the Docker Compose configuration** (inside `roles/monitoring/files/docker-compose.yaml`)

### **3. Add Hosts to Inventory**

1. **Edit the 'inventory/hosts.yaml' file to add new servers to the inventory**.
2. **Add the server to a group all or monitoring in `inventory/hosts.yaml`**

```yaml
---
# ➜ `all` group includes all servers (defined in site.yaml)
# ➜ `monitoring` group consists of servers related to monitoring (defined in monitoring.yaml)
all:
  vars:
    ansible_user: ubuntu  # Default SSH user
    ansible_ssh_private_key_file: ~/.ssh/id_rsa  # Path to private key

  children:
    monitoring:
      hosts:
        test-monitoring-1:
          ansible_host: 188.32.160.250  # Server IP address or FQDN
```

### **4. Run Ansible Playbooks**

#### 🔄 Automatic Deployment via GitHub Actions
Each push to `main` triggers the CI/CD workflow:
- **Linting code for readability**
- **Installing Ansible and dependencies**
- **Connecting to the server via SSH**
- **Running `ansible-playbook` for deployment**

#### 🚀  Run Locally

Install **Dependencies**:
```bash
sudo apt update  # Update package lists
sudo apt install -y python3 python3-pip ansible  # Install required packages
pip3 install docker passlib  # Install Python dependencies

```
Deploy **all basic roles**:
```bash
ansible-playbook site.yaml  # Deploy basic configurations
```
Deploy **only monitoring**:
```bash
ansible-playbook monitoring.yaml  # Deploy monitoring stack
```
