# Section 04 — Variables and Facts

> **Exam Objective:** Use variables and facts to manage playbook behavior dynamically.

---

## Q1. Variable types and precedence

List **5 ways** to define variables in Ansible, ordered from lowest to highest precedence.

---

## Q2. host_vars and group_vars

Create the following variable files:

- `group_vars/webservers.yml` — defines `http_port: 80` and `max_clients: 200`
- `host_vars/node3.yml` — defines `db_port: 3306` and `db_name: "appdb"`

Write a playbook `show_vars.yml` that prints all four variables on the appropriate hosts using `ansible.builtin.debug`.

---

## Q3. Register and use output

Write a playbook that:

1. Runs `df -h /` on all managed nodes
2. Registers the output into a variable called `disk_info`
3. Prints only the `stdout` of the command using `debug`

---

## Q4. set_fact

Write a task that computes a variable `free_gb` by dividing `ansible_memfree_mb` by 1024 (using Jinja2 arithmetic) and registers it as a host fact using `set_fact`.

---

## Q5. Magic variables — hostvars

Write a task that prints the `ansible_default_ipv4.address` of **every host in the inventory** from a single play targeting the control node only. Use `hostvars` and a loop.

---

## Q6. vars_files

Move the variables from Q2 into a separate file `vars/common.yml`. Update `show_vars.yml` to load them using `vars_files`.

---

## Q7. Variable precedence — override at runtime

Given that `http_port` is set to `80` in `group_vars/webservers.yml`, show the command-line syntax to override it to `8080` at runtime without modifying any file.

---

## Q8. Conditional variable assignment with default filter

Write a Jinja2 expression that returns the value of `custom_port` if defined, or defaults to `80` if not defined. Use the `default` filter.

---

## Q9. Facts — custom facts

Explain how to create a **custom fact** on a managed node. Create a fact file at `/etc/ansible/facts.d/app.fact` that exposes `app_version=2.1` and `app_env=production`. Show the task that deploys it and the task that reads it back.

---

## Q10. Facts caching

What is **fact caching** and why would you enable it? Show the `ansible.cfg` configuration to enable JSON file-based fact caching with a 24-hour timeout.

---
---
