# Section 05 — Task Control


> **Exam Objective:** Use loops, conditionals, handlers, tags, and error-handling directives.

---

## Q1. Loop — with_items vs loop

Rewrite the following task using the modern `loop` directive:

```yaml
- name: Install packages
  ansible.builtin.dnf:
    name: "{{ item }}"
    state: present
  with_items:
    - httpd
    - vim
    - git
```

---

## Q2. Loop with dictionaries

Write a task that creates three users using a single `loop` over a list of dictionaries:

| username | uid  | group   |
|----------|------|---------|
| alice    | 2001 | dev     |
| bob      | 2002 | dev     |
| carol    | 2003 | ops     |

---

## Q3. Conditionals — when

Write a task that installs `httpd` **only** when the managed node is running RHEL or AlmaLinux (not any other distribution). Use an Ansible fact in the `when` condition.

---

## Q4. Conditionals — combining conditions

Write a task that runs only when **both** of the following are true:

- The OS family is `RedHat`
- The available memory (`ansible_memfree_mb`) is greater than 512 MB

---

## Q5. Block, rescue, always

Write a play using `block` / `rescue` / `always` that:

1. Attempts to install and start `nginx`
2. On failure, prints the message `"nginx install failed, falling back"` and installs `httpd` instead
3. Always prints `"Task block complete"` regardless of success or failure

---

## Q6. Handlers — notify multiple

Write a playbook where a single task notifies **two handlers**: one that restarts `httpd` and one that reloads `firewalld`. Both handlers should only run at the end of the play.

---

## Q7. Tags — skipping tasks

Given a playbook with tasks tagged `install`, `configure`, and `test`, show the commands to:

1. Run only `install` tasks
2. Skip `test` tasks and run everything else
3. List all available tags without running the playbook

---

## Q8. Error handling — failed_when

Write a task that runs `grep "ERROR" /var/log/app.log` and marks the task as **failed** only if the return code is `0` (i.e., the word "ERROR" was found). Use `failed_when`.

---

## Q9. Retries and delay

Write a task that waits for port `80` to become open on `node1`, retrying up to **10 times** with a **5-second delay** between attempts.

---

## Q10. Loop control — loop_var and label

You have a loop that deploys 10 config files. Show how to:

1. Rename the loop variable from `item` to `config_file`
2. Suppress verbose output by showing only the filename (not the full dict) using `label`

---
---
