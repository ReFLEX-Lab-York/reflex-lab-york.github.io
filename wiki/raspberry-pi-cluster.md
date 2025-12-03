---
layout: default
title: Raspberry Pi Cluster
nav_order: 6
toc: true
---

# Raspberry Pi Cluster

The Raspberry Pi cluster is a collection of Raspberry Pi 3 Compute Modules single-board computers networked together to create a low-cost, energy-efficient computing cluster. These clusters are excellent for learning distributed computing, parallel processing, and cluster management.

## Overview

Our Raspberry Pi cluster provides a platform for:
- Distributed computing experiments
- Container orchestration (Docker, Kubernetes)
- Parallel processing tasks
- Educational projects in cluster computing
- Testing scalable applications

## Cluster Specifications

### Hardware Configuration

#### Nodes
- Number of Nodes: 40
- Model: Raspberry Pi 3 Model B
- CPU: ARM Cortex-A72 (4 cores) @ 1.5GHz per node
- Total CPU Cores: 160 cores (40 nodes × 4 cores)
- RAM: 8GB per node
- Total RAM: 320GB (40 nodes × 8GB)
- Storage: 32GB microSD card per node
- Total Storage: 1.28TB (40 nodes × 32GB)

#### Power Supply
- Type: 12V 20A × 2 units
- Total Power: 480W (2 × 12V × 20A)
- Power per Node: 5V/3A USB-C (converted from 12V)
- Max Power Consumption: ~15W per node
- Total Max Consumption: ~600W (40 nodes × 15W)


## Cluster Architecture

The cluster uses the **pakupi** setup framework, which defines three distinct roles:

- **Host**: The machine used to configure the cluster (typically your workstation)
- **Controller**: The main node providing cluster services (scheduling, file sharing, job submission, dashboard)
- **Worker**: Nodes that provide processing power to the cluster (40 Raspberry Pi nodes)

> **Note**: This guide is based on Ubuntu 22.04 Server. The Controller and Workers should use the same Linux distribution to ensure Slurm compatibility.

## Use Cases
### 1. Distributed Computing
- MapReduce implementations
- Parallel processing tasks
- Scientific computations
- Slurm job scheduling

### 2. Container Orchestration
- Kubernetes cluster for microservices
- Docker Swarm for container management
- CI/CD pipeline testing

### 3. Educational Projects
- Learning cluster architecture
- Distributed systems coursework
- Network programming experiments

### 4. Web Services
- Load-balanced web applications
- Database clusters (MySQL, PostgreSQL)
- Message queue systems (RabbitMQ, Kafka)

## Initial Setup

### Preparing the Host Machine

The Host machine is used to configure the cluster through Ansible. It requires:

1. **Install Ansible**:
   ```bash
   apt install ansible
   ansible --version  # Verify installation
   ```

2. **Install pakupi Ansible requirements**:
   ```bash
   ansible-galaxy install -r requirements-galaxy.yml
   ```

3. **Prepare SSH key**:
   ```bash
   ssh-keygen  # Generate SSH key if needed
   # Key will be in ~/.ssh/id_rsa.pub (public) and ~/.ssh/id_rsa (private)
   ```

4. **Configure cluster setup scripts** using cluster configuration variables.

### Preparing the Controller

The Controller is the main entry point for cluster interactions. Setup requirements:

#### Operating System Setup

- Install **Ubuntu 22.04 Server** (or compatible distribution)
- Configure a **fixed IP address** on the interface used for cluster communication
- Create a user with administrative rights
- Enable SSH access

#### Network Configuration

The Controller provides DHCP services for worker nodes. Example netplan configuration:

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: false
      addresses:
        - 192.168.64.1/24
```

#### Adding Controller to Inventory

Create an `inventory.ini` file with the Controller entry:

```ini
controller ansible_host=CONTROLLER_IP ansible_user=CONTROLLER_USER

[pakupi_controller]
controller

[other-group]
controller
```

### Preparing Workers

#### Operating System Setup

Each worker should run **Ubuntu 22.04 Server** (same version as Controller):

1. Flash Ubuntu Server 22.04 LTS (64-bit) to SD card
2. Create a user with administrative rights
3. Enable SSH access
4. Add Host SSH key to authorized keys (use `ssh-copy-id` if needed)

#### Network Configuration

Workers use DHCP to obtain IP addresses. Example netplan configuration:

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
```

#### Adding Workers to Inventory

1. Start the worker and check DHCP lease on Controller:
   ```bash
   dhcp-lease-list  # Run on Controller
   ```

2. Add worker to `inventory.ini`:
   ```ini
   controller ansible_host=CONTROLLER_IP ansible_user=CONTROLLER_USER
   worker1 ansible_host=WORKER1_IP ansible_user=WORKER_USER macaddress=11:22:33:44:55:66

   [pakupi_controller]
   controller

   [pakupi_worker]
   worker1
   ```

3. Connect via SSH to accept host fingerprint before running Ansible commands.

## Cluster Configuration

The cluster is configured using Ansible playbooks in the following order:

### 1. DHCP Server Setup

```bash
ansible-playbook -i inventory.ini 01-dhcp.yaml
```

**Verification**:
```bash
# Check DHCP server status
ansible -C -i inventory.ini -m service -a "state=stopped name=isc-dhcp-server" pakupi_controller
# Or on Controller directly:
systemctl status isc-dhcp-server

# Check worker IP assignment
ip addr show eth0  # On worker
dhclient eth0      # Refresh DHCP lease if needed
```

### 2. NIS (Network Information Service) Setup

```bash
ansible-playbook -i inventory.ini 02-nis.yaml
```

**Verification**:
```bash
# Check NIS server status
ansible -C -i inventory.ini -m service -a "state=stopped name=ypserv" pakupi_controller
# Test NIS on worker
yptest
```

### 3. Slurm Setup

```bash
ansible-playbook -i inventory.ini 03-slurm.yaml
```

**Verification**:
```bash
# Test Slurm on Controller
scontrol ping

# Test Slurm on Worker
scontrol ping

# Check cluster nodes
sinfo

# Test job allocation
srun hostname
```

### 4. NFS (Network File System) Setup

```bash
ansible-playbook -i inventory.ini 04-nfs.yaml
```

**Verification**:
```bash
# Check shared directories exist
ls -l /exports/pakupi        # On Controller
ls -l /shared/pakupi         # On Workers

# Test file sharing
touch /shared/pakupi/test    # On Worker
ls -l /shared/pakupi/test    # Should appear on Controller and other Workers
```

### 5. Worker Configuration

```bash
ansible-playbook -i inventory.ini 05-configure_worker.yaml
```

**Verification**:
```bash
# Check CPU frequency settings (should be 600MHz)
lscpu  # On Workers
```

### 6. Worker Read Protection (Optional)

To make workers read-only:
```bash
ansible-playbook -i inventory.ini util-worker_readonly.yaml
```

To restore read-write access:
```bash
ansible-playbook -i inventory.ini util-worker_readwrite.yaml
```

## User Management

### Adding Users

Users are managed through NIS and can be added using Ansible:

```bash
ansible-playbook -i inventory.ini util-adduser.yaml \
  -e "pakupi_user=USERNAME pakupi_password=PASSWORD"
```

Replace `USERNAME` and `PASSWORD` with the desired credentials.

After adding a user:
- The user account is created on the Controller
- NIS maps are rebuilt to propagate changes to all Workers
- The user can log in to any node in the cluster

### User Utilities

- **No password sudo**: Apply passwordless sudo access:
  ```bash
  ansible-playbook -i inventory.ini util-nopasswd.yaml
  ```

## Dashboard Configuration

### Grafana Setup

```bash
ansible-playbook -i inventory.ini 06-dashboard.yaml
```

### Accessing Grafana

1. Navigate to `http://CONTROLLER_IP:3000`
2. Default credentials:
   - Username: `admin`
   - Password: `admin`

### Configuring Slurm Dashboard

1. **Add Prometheus Data Source**:
   - Go to `Configuration > Data Sources`
   - Add Prometheus data source
   - URL: `http://localhost:9090`
   - Save and test

2. **Import Slurm Dashboard**:
   - Go to `Dashboards > Import`
   - Import by URL: `https://grafana.com/grafana/dashboards/4323`
   - Select the configured Prometheus data source

## Configuration Variables

Cluster configuration is managed through variables in the Ansible inventory and playbooks. Key configuration areas include:

- **Network settings**: IP ranges, subnet masks, DHCP ranges
- **Slurm configuration**: Partition settings, node specifications
- **NFS exports**: Shared directory paths and permissions
- **User management**: Default groups, home directory locations
- **Dashboard settings**: Grafana and Prometheus configuration

Refer to the `requirements-galaxy.yml` and playbook files for detailed configuration options.

## Resources

### Documentation
- [pakupi Setup Instructions](https://github.com/Gaudeval/pakupi/wiki/Setup-instructions)
- [pakupi Configuration Variables](https://github.com/Gaudeval/pakupi/wiki/Configuration-Variables)
- [pakupi User Management](https://github.com/Gaudeval/pakupi/wiki/User-management)
- [Raspberry Pi Official Documentation](https://www.raspberrypi.org/documentation/)
- [Ubuntu Server Installation Guide](https://ubuntu.com/tutorials/install-ubuntu-server)
- [Slurm Documentation](https://slurm.schedmd.com/documentation.html)

## Contact

For questions or issues with the RPi cluster, please contact the lab administrator (xiaotian.dai@york.ac.uk).
