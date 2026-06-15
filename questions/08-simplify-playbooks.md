# Section 08 — Simplify Playbooks with Roles


> **Exam Objective:** Create and use roles; install roles from Ansible Galaxy.

---

## Q1. Role directory structure

Describe the full directory structure of an Ansible role. What is the purpose of each subdirectory?

---

## Q2. Create a role with ansible-galaxy

Use `ansible-galaxy` to initialize a new role called `webserver` at `~/roles/webserver`. Then populate it so that it:

- Installs `httpd`
- Deploys `templates/index.html.j2` to `/var/www/html/index.html`
- Enables and starts `httpd`
- Defines a default variable `http_port: 80`

---

## Q3. Use a role in a playbook

Write a playbook `deploy_web.yml` that applies the `webserver` role to the `webservers` group and overrides `http_port` to `8080`.

---

## Q4. Role dependencies

Add a dependency to the `webserver` role so that the `firewall` role (assumed to exist at `~/roles/firewall`) runs first.  
Where is this defined, and what is the syntax?

---

## Q5. Install a role from Ansible Galaxy

Write a `requirements.yml` file that installs:

- `geerlingguy.haproxy` from Ansible Galaxy
- A role from a GitHub URL: `https://github.com/example/nginx-role`

Show the command to install all roles from this file.

---

## Q6. Install a collection

Write a `requirements.yml` that installs the `ansible.posix` collection (version `>= 1.4.0`).  
Show the install command and confirm the collection is installed.

---
---
