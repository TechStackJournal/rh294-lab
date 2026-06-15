# Section 02 — Run Ad Hoc Commands

> **Exam Objective:** Use ad hoc commands to perform one-off tasks on managed nodes
> using the `ansible` command-line tool.

---

## Q1. Basic syntax

What is the general syntax of an ad hoc Ansible command?  
Identify what each flag (`-i`, `-m`, `-a`, `-b`, `-u`) does.

---

## Q2. Ping all hosts

Run an ad hoc command to verify connectivity (using the `ping` module) against **all** hosts in your inventory.

---

## Q3. Run a shell command

Use an ad hoc command to run `uptime` on all nodes in the `managed` group.  
Use the correct module (not `shell` — which module is preferred and why?).

---

## Q4. Install a package

Use a single ad hoc command to install the `vim` package on all `webservers` using the appropriate package manager module for RHEL 9.

Requirements:
- Use privilege escalation
- Ensure the package is at the latest version

---

## Q5. Manage services

Write ad hoc commands to:

1. Start and enable `httpd` on all `webservers`
2. Stop and disable `firewalld` on `node3` only
3. Restart `sshd` on all managed nodes

---

## Q6. Manage files and directories

Write ad hoc commands to:

1. Create the directory `/opt/rh294` with mode `0755` on all managed nodes
2. Create an empty file `/opt/rh294/marker` on `node1` only
3. Remove the file `/opt/rh294/marker` from `node1`

---

## Q7. Copy a file

Use an ad hoc command to copy your local `/etc/hosts` file to `/tmp/hosts_backup` on all managed nodes.

---

## Q8. Manage users

Write ad hoc commands to:

1. Create a user `devops` with UID `2001` on all managed nodes
2. Set the user's shell to `/bin/bash`
3. Add the user to the `wheel` group

Can you do all three in a single ad hoc command? If yes, show how. If no, explain why.

---

## Q9. Gather a specific fact

Use an ad hoc command to retrieve only the `ansible_distribution` and `ansible_distribution_version` facts from `node2`. Use the `setup` module with a filter.

---

## Q10. Run with check mode

Re-run the package install command from Q4 in **check mode**. What does check mode do, and why is it useful before making changes in production?
