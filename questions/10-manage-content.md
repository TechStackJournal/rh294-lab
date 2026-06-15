# Section 10 — Manage Content with Collections


> **Exam Objective:** Use ansible-galaxy to manage roles and collections; use content from collections.

---

## Q1. What is a collection?

Explain the difference between an Ansible **role** and an Ansible **collection**. What types of content can a collection contain?

---

## Q2. Install a collection

Install the `community.general` collection and verify it is installed. Where are collections stored by default?

---

## Q3. Use a collection module in a playbook

Write a playbook that uses `community.general.github_release` to check for the latest release of a GitHub repository. Use the **Fully Qualified Collection Name (FQCN)** for the module.

---

## Q4. requirements.yml — roles and collections

Write a single `requirements.yml` that installs both:

- Role: `geerlingguy.mysql`
- Collection: `ansible.posix` (version `>= 1.5.0`)

Show the single `ansible-galaxy` command that installs both.

---

## Q5. Create a collection skeleton

Use `ansible-galaxy collection init` to scaffold a new collection named `myorg.myapp`. Describe what files are created and what each is for.

---

## Q6. Using `ansible.posix.firewalld`

Write a playbook that uses `ansible.posix.firewalld` to:

1. Open `http` and `https` services permanently on all `webservers`
2. Open port `3306/tcp` on `dbservers`
3. Reload the firewall after changes

Ensure the `ansible.posix` collection is referenced by FQCN throughout.
