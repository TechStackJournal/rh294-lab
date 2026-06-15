# Solutions — Section 09: Ansible Vault

---

## A1. Create encrypted vault file

```bash
ansible-vault create vault/secrets.yml
```

Enter a vault password when prompted, then type:

```yaml
db_password: "S3cur3P@ssw0rd"
api_key: "abc123xyz"
```

---

## A2. Edit encrypted file

```bash
ansible-vault edit vault/secrets.yml
```

---

## A3. Use vault in a playbook

```yaml
# deploy_secrets.yml
---
- name: Deploy app secrets
  hosts: dbservers
  become: true
  vars_files:
    - vault/secrets.yml

  tasks:
    - name: Create .env file
      ansible.builtin.copy:
        content: |
          DB_PASSWORD={{ db_password }}
          API_KEY={{ api_key }}
        dest: /opt/app/.env
        owner: root
        mode: '0600'
```

```bash
# Create password file
echo "myvaultpassword" > ~/.vault_pass
chmod 600 ~/.vault_pass

# Run playbook using password file
ansible-playbook deploy_secrets.yml --vault-password-file ~/.vault_pass
```

---

## A4. Inline encrypted variable

```bash
ansible-vault encrypt_string 'S3cur3P@ssw0rd' --name 'db_password'
```

Output (example):

```yaml
db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          38326135643664633937373437326464...
```

Use it in a playbook:

```yaml
vars:
  db_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
    38326135643664633937373437326464...

tasks:
  - ansible.builtin.debug:
      var: db_password
```

---

## A5. Rekey vault file

```bash
ansible-vault rekey vault/secrets.yml
# Prompted for: current password, then new password
```

---

## A6. Multiple vault IDs

```bash
# Encrypt with vault IDs
ansible-vault encrypt --vault-id dev@prompt vault/dev_secrets.yml
ansible-vault encrypt --vault-id prod@prompt vault/prod_secrets.yml

# Run playbook supplying both
ansible-playbook site.yml \
  --vault-id dev@~/.vault_pass_dev \
  --vault-id prod@~/.vault_pass_prod
```

Each encrypted file carries its vault ID label in the header, so Ansible knows which password to use for decryption.

---
---
