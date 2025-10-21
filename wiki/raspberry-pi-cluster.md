---
layout: default
title: Raspberry Pi Cluster
nav_order: 4
toc: true
---

# Raspberry Pi Cluster

A Raspberry Pi cluster is a collection of Raspberry Pi single-board computers networked together to create a low-cost, energy-efficient computing cluster. These clusters are excellent for learning distributed computing, parallel processing, and cluster management.

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
- **Number of Nodes**: 40
- **Model**: Raspberry Pi 4 Model B
- **CPU**: ARM Cortex-A72 (4 cores) @ 1.5GHz per node
- **Total CPU Cores**: 160 cores (40 nodes × 4 cores)
- **RAM**: 8GB per node
- **Total RAM**: 320GB (40 nodes × 8GB)
- **Storage**: 32GB microSD card per node
- **Total Storage**: 1.28TB (40 nodes × 32GB)

#### Networking
- **Network Switch**: TBD
- **Connection**: Gigabit Ethernet
- **Topology**: Star topology with central switch

#### Power Supply
- **Type**: 12V 20A × 2 units
- **Total Power**: 480W (2 × 12V × 20A)
- **Power per Node**: 5V/3A USB-C (converted from 12V)
- **Max Power Consumption**: ~15W per node
- **Total Max Consumption**: ~600W (40 nodes × 15W)

### Software Stack

#### Operating System
- **OS**: Raspberry Pi OS (64-bit) or Ubuntu Server
- **Kernel**: Linux ARM64

#### Container Platform
- **Docker**: For containerized applications
- **Kubernetes**: For container orchestration (k3s or MicroK8s)

#### Cluster Management
- **Cluster Manager**: TBD (Kubernetes, Docker Swarm, or custom)
- **Monitoring**: Prometheus + Grafana (optional)

## Use Cases

### 1. Distributed Computing
- MapReduce implementations
- Parallel processing tasks
- Scientific computations

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

## Getting Started

### Accessing the Cluster

*Access instructions to be added*

### Basic Commands

```bash
# SSH into the master node
ssh pi@cluster-master.local

# Check cluster status
kubectl get nodes

# View running pods
kubectl get pods --all-namespaces

# Deploy an application
kubectl apply -f deployment.yaml
```

## Resources

### Documentation
- [Raspberry Pi Official Documentation](https://www.raspberrypi.org/documentation/)
- [Kubernetes on Raspberry Pi](https://kubernetes.io/)
- [Docker ARM Documentation](https://docs.docker.com/)

### Tutorials
- Building a Raspberry Pi cluster from scratch
- Setting up Kubernetes on ARM
- Distributed computing with MPI

## Maintenance

### Regular Tasks
- Update system packages
- Monitor node health
- Check network connectivity
- Backup configurations

### Troubleshooting
- Node offline: Check power and network connections
- Performance issues: Monitor CPU and memory usage
- Network problems: Verify switch configuration

## Future Enhancements

- [ ] Add more nodes to increase capacity
- [ ] Implement distributed storage (Ceph, GlusterFS)
- [ ] Set up monitoring dashboard
- [ ] Install GPU accelerators for ML workloads
- [ ] Configure automated backups

## Contact

For questions or issues with the Raspberry Pi cluster, please contact the lab administrator.

