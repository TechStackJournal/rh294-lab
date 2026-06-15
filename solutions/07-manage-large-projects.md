# Solutions — Section 07: Manage Large Projects

---

## A1. include_tasks vs import_tasks

| | `include_tasks` | `import_tasks` |
|-|----------------|---------------|
| Processed | At runtime (dynamic) | At parse time (static) |
| Supports `when` on the directive | Yes | Yes (but evaluated at parse) |
| Supports `loop` on the directive | Yes | No |
| Tags on included tasks | Must use `--tags all` | Inherited automatically |
| Use when | Conditional/looped includes | Always-needed task sets |

---

## A2. include_tasks with loop

```yaml
# tasks/create_user.yml
- name: Create user {{ username }}
  ansible.builtin.user:
    name: "{{ username }}"
    state: present
```

```yaml
# playbook
- name: Create multiple users
  hosts: managed
  tasks:
    - name: Create users via include
      ansible.builtin.include_tasks: tasks/create_user.yml
      loop:
        - alice
        - bob
        - carol
      loop_control:
        loop_var: username
```

---

## A3. import_playbook

```yaml
# site.yml
---
- name: Import web playbook
  ansible.builtin.import_playbook: playbooks/webservers.yml

- name: Import db playbook
  ansible.builtin.import_playbook: playbooks/dbservers.yml
```

---

## A4. Dynamic include based on OS

```yaml
- name: Include OS-specific tasks
  ansible.builtin.include_tasks: "tasks/{{ ansible_os_family | lower }}.yml"
```

This dynamically selects `tasks/redhat.yml` or `tasks/debian.yml` at runtime.

---

## A5. Inventory directory

```
inventory/
├── hosts.ini          # Static host definitions
├── group_vars/
│   ├── all.yml
│   ├── webservers.yml
│   └── dbservers.yml
└── host_vars/
    ├── node1.lab.example.com.yml
    └── node3.lab.example.com.yml
```

Point `ansible.cfg` to the directory: `inventory = ~/inventory`

---
---
