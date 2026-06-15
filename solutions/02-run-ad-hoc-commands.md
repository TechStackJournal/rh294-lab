# Solutions — Section 02: Run Ad Hoc Commands

---

## A1. Basic syntax

```
ansible <host-pattern> -i <inventory> -m <module> -a "<arguments>" [options]
```

| Flag | Meaning |
|------|---------|
| `-i` | Specify inventory file or directory |
| `-m` | Module name to use |
| `-a` | Module arguments (key=value pairs) |
| `-b` | Become (privilege escalation, default sudo) |
| `-u` | Remote user to connect as |

---

## A2. Ping all hosts

```bash
ansible all -m ping
```

---

## A3. Run a shell command — correct module

```bash
ansible managed -m ansible.builtin.command -a "uptime"
```

Use `ansible.builtin.command` over `shell` because:
- `command` does not invoke a shell, reducing injection risk
- `command` is idempotent-friendly (no pipes/redirects that shell needs)
- Use `shell` only when you need shell features: pipes (`|`), redirects (`>`), `&&`, etc.

---

## A4. Install a package

```bash
ansible webservers -m ansible.builtin.dnf -a "name=vim state=latest" -b
```

---

## A5. Manage services

```bash
# 1. Start and enable httpd on webservers
ansible webservers -m ansible.builtin.service \
  -a "name=httpd state=started enabled=true" -b

# 2. Stop and disable firewalld on node3 only
ansible node3.lab.example.com -m ansible.builtin.service \
  -a "name=firewalld state=stopped enabled=false" -b

# 3. Restart sshd on all managed nodes
ansible managed -m ansible.builtin.service \
  -a "name=sshd state=restarted" -b
```

---

## A6. Manage files and directories

```bash
# 1. Create directory /opt/rh294 on all managed nodes
ansible managed -m ansible.builtin.file \
  -a "path=/opt/rh294 state=directory mode=0755" -b

# 2. Create empty file on node1
ansible node1.lab.example.com -m ansible.builtin.file \
  -a "path=/opt/rh294/marker state=touch" -b

# 3. Remove the file from node1
ansible node1.lab.example.com -m ansible.builtin.file \
  -a "path=/opt/rh294/marker state=absent" -b
```

---

## A7. Copy a file

```bash
ansible managed -m ansible.builtin.copy \
  -a "src=/etc/hosts dest=/tmp/hosts_backup" -b
```

---

## A8. Manage users

```bash
# Individual commands
ansible managed -m ansible.builtin.user \
  -a "name=devops uid=2001 shell=/bin/bash groups=wheel append=yes" -b
```

**Yes**, all three requirements (name, uid, shell, group) can be set in a single ad hoc command because the `user` module accepts all of these as parameters simultaneously, as shown above. The `append=yes` ensures the user is **added** to `wheel` without removing existing groups.

---

## A9. Gather specific facts with filter

```bash
ansible node2.lab.example.com -m ansible.builtin.setup \
  -a "filter=ansible_distribution,ansible_distribution_version"
```

---

## A10. Check mode

```bash
ansible webservers -m ansible.builtin.dnf \
  -a "name=vim state=latest" -b --check
```

**Check mode** (`--check`) runs the module in a dry-run — it reports what **would** change without actually making changes. It is useful to:

- Preview the impact of a change before applying it
- Audit drift between expected and actual state
- Test playbooks in CI pipelines without modifying systems

> Note: Some modules do not fully support check mode and may report inaccurate results.
