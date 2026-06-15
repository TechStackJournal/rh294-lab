# Solutions — Section 03: Implement Playbooks

---

## A1. Fixed playbook

Errors found:
1. `ansible.builtin.dnf` — missing `:` colon after the module name
2. `enabled: true` — indented as a sibling of `service` task, should be a parameter of `ansible.builtin.service`

```yaml
---
- name: Fixed playbook
  hosts: webservers
  tasks:
    - name: Install httpd
      ansible.builtin.dnf:          # <-- colon added
        name: httpd
        state: present

    - name: Start httpd
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: true               # <-- moved inside the service task
```

---

## A2. install_web.yml

```yaml
---
- name: Deploy web server
  hosts: webservers
  become: true

  handlers:
    - name: Restart httpd
      ansible.builtin.service:
        name: httpd
        state: restarted

  tasks:
    - name: Install httpd and firewalld
      ansible.builtin.dnf:
        name:
          - httpd
          - firewalld
        state: present
      notify: Restart httpd

    - name: Enable and start httpd
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: true

    - name: Enable and start firewalld
      ansible.builtin.service:
        name: firewalld
        state: started
        enabled: true

    - name: Open port 80/tcp in firewalld
      ansible.posix.firewalld:
        port: 80/tcp
        permanent: true
        state: enabled
        immediate: true
```

---

## A3. site.yml — multiple plays

```yaml
---
- name: Deploy web servers
  hosts: webservers
  become: true
  tasks:
    - name: Install httpd
      ansible.builtin.dnf:
        name: httpd
        state: present

    - name: Start and enable httpd
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: true

- name: Deploy database servers
  hosts: dbservers
  become: true
  tasks:
    - name: Install mariadb-server
      ansible.builtin.dnf:
        name: mariadb-server
        state: present

    - name: Start and enable mariadb
      ansible.builtin.service:
        name: mariadb
        state: started
        enabled: true
```

---

## A4. Tags

```yaml
---
- name: Deploy web server with tags
  hosts: webservers
  become: true

  tasks:
    - name: Install httpd and firewalld
      ansible.builtin.dnf:
        name:
          - httpd
          - firewalld
        state: present
      tags: install

    - name: Enable and start services
      ansible.builtin.service:
        name: "{{ item }}"
        state: started
        enabled: true
      loop:
        - httpd
        - firewalld
      tags: service

    - name: Open port 80/tcp
      ansible.posix.firewalld:
        port: 80/tcp
        permanent: true
        state: enabled
        immediate: true
      tags: firewall
```

Run only `install` tasks:

```bash
ansible-playbook install_web.yml --tags install
```

---

## A5. Handlers vs Tasks

A **task** runs unconditionally (subject to `when` conditions) during each play execution.

A **handler** is triggered by a task's `notify` directive and runs **once at the end of the play** — regardless of how many tasks notified it.

A handler does **NOT** run even if notified when:
- The play fails before reaching the end (unless `--force-handlers` is passed)
- The `meta: flush_handlers` step is not used and the play aborts early
- The task that would notify it is skipped (so the notify is never triggered)

---

## A6. Check mode and diff mode

```bash
# Check mode only — shows what would change, no changes made
ansible-playbook install_web.yml --check

# Check mode + diff — also shows the exact content differences
ansible-playbook install_web.yml --check --diff
```

- `--check`: Reports task status (changed/ok/skipped) without modifying the system
- `--diff`: Shows a unified diff of file content changes (very useful with `template` and `copy` tasks)
- Combined: Preview both which tasks would run AND exactly what file changes would occur

---

## A7. Limiting hosts at runtime

```bash
ansible-playbook site.yml --limit "node1.lab.example.com,node3.lab.example.com"
# OR using short names if they resolve:
ansible-playbook site.yml --limit "node1,node3"
```

---

## A8. Playbook verbosity levels

| Level | Added information |
|-------|------------------|
| `-v` | Task results (return values) |
| `-vv` | Task input (module arguments) |
| `-vvv` | Connection details (SSH commands, paths) |
| `-vvvv` | Plugin and callback loader debug info, raw SSH output |

---

## A9. Idempotency

**Idempotency** means running the same operation multiple times produces the same result as running it once — it only makes changes when the current state differs from the desired state.

First run: tasks show `changed` (packages installed, services started).  
Second run: tasks show `ok` — no changes because the desired state already exists.

This is a core principle: Ansible playbooks are safe to re-run.

---

## A10. ignore_errors

```yaml
    - name: Install httpd
      ansible.builtin.dnf:
        name: httpd
        state: present
      ignore_errors: true
```

> Note: `ignore_errors: true` allows the playbook to continue past this task even if it fails. The failed task still shows as `failed` in the output, but execution proceeds.
