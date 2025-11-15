---
layout: default
title: Lab Server
nav_order: 1
parent: Computing Resources
toc: true
---

# Lab Server (`reflex-server`)
## Booking

As this is a shared machine, to avoid conflicts, you have to use [this spreadsheet](https://docs.google.com/spreadsheets/d/1W5mpbAEKUQXTg1ZdBDx6h2xm0srli53Eu4JJge7ovd8/edit?gid=0#gid=0) to check the availability and book timeslots.

---

## How to Use

1. To use the server, please first download [RustDesk](https://rustdesk.com/).
2. Open RustDesk using a login ID of `207 686 592`
3. Then use it as if it were a local machine.
4. Just close the window after your use. Your code will keep running.

---

## Hardware
### CPU

- Intel Core i5-6700k, 4C8T @ 4GHz

### RAM

- 32 GB (8 GB x 4), DDR 4

### GPU

- Radeon RX 580, with 8 GB of VRAM

### Hard Drives

- 500 GB, M.2 SSD, `/dev/sdd`, mount point: `/`
- 1 TB, NVME SSD, `/dev/nvme0`, mount point: `/mnt/nvme0/`
- 1 TB, HDD, `/dev/sda`, mount point: `/mnt/sda/`
- 256 GB, SATA SSD, `/dev/sdb`, mount point: `/mnt/sdb/`
- 500 GB, SATA SSD, `/dev/sdc`, mount point: `/mnt/sdc/`

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
