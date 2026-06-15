# Solutions — Section 10: Manage Content with Collections

---

## A1. Role vs Collection

| | Role | Collection |
|-|------|-----------|
| Contains | Tasks, handlers, templates, vars | Modules, plugins, roles, playbooks, docs |
| Scope | Single automation unit | Namespace of related content |
| Distributed via | Ansible Galaxy | Ansible Galaxy / Automation Hub |
| FQCN example | (no FQCN for standalone roles) | `ansible.posix.firewalld` |

---

## A2. Install community.general

```bash
ansible-galaxy collection install community.general
ansible-galaxy collection list community.general   # verify
```

Default install path: `~/.ansible/collections/ansible_collections/`

---

## A3. community.general.github_release

```yaml
---
- name: Check latest GitHub release
  hosts: localhost
  gather_facts: false
  tasks:
    - name: Get latest release info
      community.general.github_release:
        user: ansible
        repo: ansible
        action: latest_release
      register: release

    - name: Print release
      ansible.builtin.debug:
        var: release.tag
```

---

## A4. Combined requirements.yml

```yaml
---
roles:
  - name: geerlingguy.mysql

collections:
  - name: ansible.posix
    version: ">=1.5.0"
```

```bash
ansible-galaxy install -r requirements.yml
```

> Note: `ansible-galaxy install` handles both roles and collections when using `-r`.

---

## A5. Collection skeleton

```bash
ansible-galaxy collection init myorg.myapp
```

Created structure:

```
myorg/myapp/
├── galaxy.yml          # Collection metadata (name, version, author, deps)
├── README.md
├── plugins/
│   ├── modules/        # Custom modules (.py files)
│   ├── inventory/      # Inventory plugins
│   └── filter/         # Jinja2 filter plugins
├── roles/              # Roles bundled in the collection
├── playbooks/          # Playbooks distributed with the collection
└── tests/
    └── integration/    # Integration tests
```

---

## A6. ansible.posix.firewalld playbook

```yaml
---
- name: Configure firewall on web servers
  hosts: webservers
  become: true
  tasks:
    - name: Open http and https services
      ansible.posix.firewalld:
        service: "{{ item }}"
        permanent: true
        state: enabled
        immediate: true
      loop:
        - http
        - https

- name: Configure firewall on db servers
  hosts: dbservers
  become: true
  tasks:
    - name: Open MySQL port
      ansible.posix.firewalld:
        port: 3306/tcp
        permanent: true
        state: enabled
        immediate: true
```
