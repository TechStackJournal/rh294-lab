# RH294 Ansible Automation Lab

> **Red Hat Certified Engineer (RHCE) — EX294 Exam Prep**
> Self-paced lab environment using Vagrant + AlmaLinux 9 (RHEL 9 compatible)

---

## Table of Contents

- [Overview](#overview)
- [Lab Topology](#lab-topology)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. Boot the Lab Environment](#2-boot-the-lab-environment)
  - [3. Push SSH Keys to Managed Nodes](#3-push-ssh-keys-to-managed-nodes)
  - [4. Verify Connectivity](#4-verify-connectivity)
- [Repository Structure](#repository-structure)
- [Exam Objectives Covered](#exam-objectives-covered)
- [How to Use This Lab](#how-to-use-this-lab)
- [Git Workflow](#git-workflow)
- [Troubleshooting](#troubleshooting)
- [Resources](#resources)

---

## Overview

This repository provides a fully self-contained Ansible lab environment aligned with the **Red Hat EX294 exam objectives**. It covers all RH294 course topics through hands-on questions and verified solutions, organized by exam section.

---

## Lab Topology

```
Host Machine (your laptop/desktop)
│
├── control.lab.example.com   192.168.56.10  (2 vCPU / 2 GB RAM)
│     └── Ansible installed, ansible user with SSH keys
│
├── node1.lab.example.com     192.168.56.11  (1 vCPU / 1 GB RAM)  [webservers]
├── node2.lab.example.com     192.168.56.12  (1 vCPU / 1 GB RAM)  [webservers]
└── node3.lab.example.com     192.168.56.13  (1 vCPU / 1 GB RAM)  [dbservers]
```

All nodes run **AlmaLinux 9** (binary-compatible RHEL 9 replacement).

---

## Prerequisites

| Tool | Minimum Version | Install |
|------|----------------|---------|
| VirtualBox | 6.1+ | https://www.virtualbox.org |
| Vagrant | 2.3+ | https://www.vagrantup.com |
| vagrant-hostmanager plugin | any | `vagrant plugin install vagrant-hostmanager` |
| Git | 2.x | `dnf/apt install git` |
| 8 GB free RAM | — | For all 4 VMs |
| 20 GB free disk | — | For box images |

---

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/rh294-lab.git
cd rh294-lab
```

### 2. Boot the Lab Environment

```bash
vagrant up
```

This will:
- Download the AlmaLinux 9 box (~1 GB, first run only)
- Create 4 VMs (control + node1/2/3)
- Install Ansible on the control node
- Create the `ansible` user on all nodes
- Write `ansible.cfg` and `inventory/hosts.ini` on the control node

### 3. Push SSH Keys to Managed Nodes

```bash
# SSH into the control node
vagrant ssh control

# Switch to the ansible user
sudo su - ansible

# Copy the public key to each managed node
# Password for vagrant user is 'vagrant'
ssh-copy-id ansible@node1.lab.example.com
ssh-copy-id ansible@node2.lab.example.com
ssh-copy-id ansible@node3.lab.example.com
```

> **Tip:** If `ssh-copy-id` asks for a password, use `vagrant`.

### 4. Verify Connectivity

```bash
# Still on control node as ansible user
ansible all -m ping
```

Expected output:

```
control.lab.example.com | SUCCESS => { "ping": "pong" }
node1.lab.example.com   | SUCCESS => { "ping": "pong" }
node2.lab.example.com   | SUCCESS => { "ping": "pong" }
node3.lab.example.com   | SUCCESS => { "ping": "pong" }
```

---

## Repository Structure

```
rh294-lab/
├── Vagrantfile                        # Lab VM definitions
├── README.md                          # This file
├── inventory/
│   └── hosts.ini                      # Static inventory (also written by Vagrant)
├── playbooks/                         # Scratch area for your playbooks
├── questions/
│   ├── 01-understand-core-components.md
│   ├── 02-run-ad-hoc-commands.md
│   ├── 03-implement-playbooks.md
│   ├── 04-variables-and-facts.md
│   ├── 05-task-control.md
│   ├── 06-deploy-files.md
│   ├── 07-manage-large-projects.md
│   ├── 08-simplify-playbooks.md
│   ├── 09-ansible-vault.md
│   └── 10-manage-content.md
└── solutions/
    ├── 01-understand-core-components/
    ├── 02-run-ad-hoc-commands/
    ├── 03-implement-playbooks/
    ├── 04-variables-and-facts/
    ├── 05-task-control/
    ├── 06-deploy-files/
    ├── 07-manage-large-projects/
    ├── 08-simplify-playbooks/
    ├── 09-ansible-vault/
    └── 10-manage-content/
```

---

## Exam Objectives Covered

| # | Section | Key Topics |
|---|---------|-----------|
| 01 | Understand Core Components | Inventories, modules, variables, facts, plays, playbooks, configuration files |
| 02 | Run Ad Hoc Commands | `ansible` CLI, `-m`, `-a`, privilege escalation |
| 03 | Implement Playbooks | YAML syntax, tasks, handlers, `ansible-playbook` |
| 04 | Variables and Facts | `vars`, `vars_files`, `host_vars`, `group_vars`, `set_fact`, `register`, magic variables |
| 05 | Task Control | Loops, conditionals, handlers, tags, error handling |
| 06 | Deploy Files | `template`, `copy`, `file`, `lineinfile`, Jinja2 |
| 07 | Manage Large Projects | Includes, imports, roles structure |
| 08 | Simplify Playbooks with Roles | `ansible-galaxy`, role structure, dependencies |
| 09 | Ansible Vault | `ansible-vault create/edit/encrypt/decrypt`, vault in playbooks |
| 10 | Manage Content with Collections | `ansible-galaxy collection install`, `requirements.yml` |

---

## How to Use This Lab

1. Read the question file for a section (e.g., `questions/03-implement-playbooks.md`)
2. Write your playbook/solution in the `playbooks/` directory
3. Run it against the lab inventory
4. Compare your result against `solutions/03-implement-playbooks/`

Work through each section sequentially — later sections build on earlier ones.

---

## Git Workflow

```bash
# Stage and commit your work
git add playbooks/my-solution.yml
git commit -m "feat: add solution for task control lab"

# Push to GitHub
git push origin main

# Create a branch for each exam section
git checkout -b section-05-task-control
```

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `vagrant up` fails on box download | Check your internet connection; try `vagrant box add almalinux/9` manually |
| `ansible all -m ping` returns `UNREACHABLE` | Verify SSH keys were copied; check `~/.ssh/known_hosts` |
| Permission denied on managed node | Confirm `/etc/sudoers.d/ansible` exists with `NOPASSWD:ALL` |
| `ansible.cfg` not found | Ensure you are running `ansible` commands as the `ansible` user in `/home/ansible` |

---

## Resources

- [Red Hat EX294 Exam Objectives](https://www.redhat.com/en/services/training/ex294-red-hat-certified-engineer-rhce-exam-red-hat-enterprise-linux-9)
- [Ansible Documentation](https://docs.ansible.com)
- [AlmaLinux Project](https://almalinux.org)
- [Vagrant Docs](https://developer.hashicorp.com/vagrant/docs)
