# Section 09 — Ansible Vault


> **Exam Objective:** Use Ansible Vault to protect sensitive data.

---

## Q1. Create an encrypted file

Create an Ansible Vault-encrypted file `vault/secrets.yml` that stores:

```yaml
db_password: "S3cur3P@ssw0rd"
api_key: "abc123xyz"
```

---

## Q2. Edit an encrypted file

Show the command to edit the vault file from Q1 without decrypting it to disk first.

---

## Q3. Use vault in a playbook

Write a playbook that:

1. Loads `vault/secrets.yml` using `vars_files`
2. Creates a file `/opt/app/.env` on `node3` containing both secret values
3. Can be run by passing the vault password via a **password file** (not interactively)

---

## Q4. Encrypt a single variable

Use `ansible-vault encrypt_string` to create an inline encrypted variable for `db_password: "S3cur3P@ssw0rd"`.  
Paste the output directly into a playbook `vars:` block and use the variable in a `debug` task.

---

## Q5. Rekeying a vault file

Show the command to change the encryption password of `vault/secrets.yml` from the old password to a new one.

---

## Q6. Multiple vault IDs

Explain **vault IDs** in Ansible. Show how to encrypt two files with different vault IDs (`dev` and `prod`) and how to supply both passwords when running a playbook.

---
---
