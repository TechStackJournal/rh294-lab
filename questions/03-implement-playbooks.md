# Section 03 — Implement Playbooks

> **Exam Objective:** Create and run Ansible playbooks; understand plays, tasks, handlers, and YAML syntax.

---

## Q1. YAML fundamentals

Identify and fix all errors in the following playbook snippet:

```yaml
---
- name: Bad playbook
  hosts: webservers
  tasks:
  - name: Install httpd
    ansible.builtin.dnf
      name: httpd
      state: present
  - name: Start httpd
    ansible.builtin.service:
      name: httpd
      state: started
    enabled: true
```

---

## Q2. Basic playbook

Write a playbook `install_web.yml` that:

1. Targets `webservers`
2. Installs `httpd` and `firewalld`
3. Enables and starts both services
4. Opens port `80/tcp` using `ansible.posix.firewalld`
5. Uses a handler to restart `httpd` only when the package state changes

---

## Q3. Multiple plays in one playbook

Write a single playbook `site.yml` with **two plays**:

- **Play 1:** Targets `webservers` — installs and starts `httpd`
- **Play 2:** Targets `dbservers` — installs and starts `mariadb-server`

---

## Q4. Run specific tasks with tags

Add tags to the `install_web.yml` playbook so that:

- The install tasks are tagged `install`
- The service tasks are tagged `service`
- The firewall task is tagged `firewall`

Show the command to run only the `install` tagged tasks.

---

## Q5. Handlers

Explain the difference between a **task** and a **handler** in Ansible. In what scenario does a handler NOT run even though it was notified?

---

## Q6. Check mode and diff mode

Run `install_web.yml` with:

1. Check mode only
2. Check mode + diff mode combined

Explain the output difference between the two.

---

## Q7. Limiting hosts at runtime

Show how to run `site.yml` but limit execution to only `node1` and `node3` using a command-line flag.

---

## Q8. Playbook verbosity

Run a playbook with maximum verbosity (`-vvvv`). What additional information does each `-v` level add compared to the previous one?

---

## Q9. Idempotency

What does **idempotency** mean in Ansible context?  
Run `install_web.yml` twice. What changed in the second run's output and why?

---

## Q10. Error handling — ignore errors

Update `install_web.yml` so that if the `httpd` package installation fails on any node, the playbook continues to the next task rather than aborting. Use the appropriate directive.
