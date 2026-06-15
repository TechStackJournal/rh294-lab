# Section 01 — Understand Core Components of Ansible

> **Exam Objective:** Understand core Ansible components including inventories, modules,
> variables, facts, plays, playbooks, and configuration files.

---

## Q1. Describe the purpose of the Ansible configuration file

What is the name of the default Ansible configuration file, and what is its default search order when Ansible looks for it?  
List at least **4 entries** in the correct priority order (highest → lowest).

---

## Q2. Inspect the active configuration

On the control node, run a single command that shows which `ansible.cfg` file is currently being used and displays its full effective configuration.

---

## Q3. Static inventory — INI format

Create a static inventory file at `~/inventory/hosts.ini` that satisfies the following:

- A group called `webservers` containing `node1` and `node2`
- A group called `dbservers` containing `node3`
- A parent group called `production` that includes both `webservers` and `dbservers`
- All hosts use the `ansible` user and key-based authentication

---

## Q4. Static inventory — YAML format

Convert the INI inventory from Q3 into a valid YAML inventory file at `~/inventory/hosts.yml`.

---

## Q5. List inventory hosts

Using ad hoc commands only (no playbook), list:

1. All hosts in the `webservers` group
2. All hosts in the `production` group
3. The total count of managed nodes in your inventory

---

## Q6. Ansible modules

Answer the following:

1. What is an Ansible **module**?
2. How do you get documentation for the `ansible.builtin.user` module without internet access?
3. What flag shows a list of available examples for a module?

---

## Q7. Understand facts

1. What are **Ansible facts**?
2. Run an ad hoc command to gather and display all facts for `node1`.
3. What single fact key contains the hostname of a managed node?
4. What fact key contains the list of all IPv4 addresses?

---

## Q8. Disable fact gathering

Write a playbook snippet (just the `hosts` + `gather_facts` lines) that disables automatic fact gathering for a play targeting the `webservers` group.

When would you want to disable fact gathering?

---

## Q9. Magic variables

Explain the purpose of the following magic variables and give a one-line example use of each:

| Variable | Purpose |
|----------|---------|
| `inventory_hostname` | |
| `ansible_host` | |
| `hostvars` | |
| `groups` | |
| `group_names` | |

---

## Q10. Ansible configuration — custom settings

Write a complete `ansible.cfg` file that sets the following:

- Default inventory: `~/inventory/hosts.ini`
- Default remote user: `ansible`
- Disable host key checking
- Enable privilege escalation with `sudo` (no password prompt)
- Set `roles_path` to `~/roles`
- Set `forks` to `10`
