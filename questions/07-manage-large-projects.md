# Section 07 — Manage Large Projects


> **Exam Objective:** Use includes, imports, and organize playbooks for maintainability.

---

## Q1. include_tasks vs import_tasks

Explain the difference between `include_tasks` and `import_tasks`. Give one scenario where each is required.

---

## Q2. include_tasks with variables

Create a task file `tasks/create_user.yml` that accepts a `username` variable and creates the user.  
Write a playbook that includes this file **three times** with different usernames using a loop.

---

## Q3. import_playbook

Create a `site.yml` playbook that imports two other playbooks:

- `playbooks/webservers.yml` — targets `webservers`
- `playbooks/dbservers.yml` — targets `dbservers`

---

## Q4. Dynamic includes — conditional

Write a play that uses `include_tasks` to dynamically include either `tasks/rhel.yml` or `tasks/ubuntu.yml` depending on `ansible_os_family`.

---

## Q5. Inventory organization — multiple files

Describe how to use a **directory as an inventory** source (instead of a single file). What files would you place in `inventory/` to separate static hosts from group variables?

---
---
