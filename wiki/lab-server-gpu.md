---
layout: default
title: Lab Server (GPU)
nav_order: 2
parent: Computing Resources
toc: true
---

# Lab Server (`reflex-server-gpu`)
## How to Use

Currently this machine is located in the Real-Time Systems Lab (CSE/128) and can only be physically accessed.

---

## Hardware
### CPU

- Intel Core i5-13600k, 14C20T @ 5.1GHz

### RAM

- 96 GB (48 GB x 2), DDR 5

### GPU

- Nvidia RTX 4070 Super, with 12 GB of VRAM

### Hard Drives

- 1 TB, NVME SSD, `/dev/nvme0`, mount point: `/`


---

## Storage
### Software Installation

All software should be installed, if possible, at: `/opt/`

### Your Working Folder

- `/home/yunfei/Workstation/<Your University ID>`
- Small but fast

### Persistent Data

- `/mnt/storage/<Your University ID>`
- Larger but slower

---

## Environment

For package management, `Docker` is encorgaged to use.
You should use either `conda` or Python `venv` to manage your Python environment. Please do not install package to the global Python.

## ⚠️ Caution

{: .warning }
> - ⚠️ Please do NOT reboot or shut down the machine. If you do need to reboot, just let me know.
> - ⚠️ Do NOT upgrade the Nvidia Driver, as it will crash everything!
