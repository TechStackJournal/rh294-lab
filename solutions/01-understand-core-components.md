# Solutions — Section 01: Understand Core Components

---

## A1. Ansible configuration file search order

Default filename: `ansible.cfg`

Search order (highest → lowest priority):

1. `ANSIBLE_CONFIG` environment variable (if set)
2. `./ansible.cfg` — current working directory
3. `~/.ansible.cfg` — user home directory
4. `/etc/ansible/ansible.cfg` — system-wide default

---

## A2. Inspect the active configuration

```bash
ansible --version
```

Or for the full effective config:

```bash
ansible-config dump --only-changed   # only non-default values
ansible-config dump                  # all effective settings
ansible-config view                  # view the raw ansible.cfg in use
```

---

## A3. Static inventory — INI format

```ini
# ~/inventory/hosts.ini

[webservers]
node1.lab.example.com ansible_host=192.168.56.11
node2.lab.example.com ansible_host=192.168.56.12

[dbservers]
node3.lab.example.com ansible_host=192.168.56.13

[production:children]
webservers
dbservers

[all:vars]
ansible_user=ansible
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

---

## A4. Static inventory — YAML format

```yaml
# ~/inventory/hosts.yml
all:
  vars:
    ansible_user: ansible
    ansible_ssh_private_key_file: ~/.ssh/id_ed25519
  children:
    webservers:
      hosts:
        node1.lab.example.com:
          ansible_host: 192.168.56.11
        node2.lab.example.com:
          ansible_host: 192.168.56.12
    dbservers:
      hosts:
        node3.lab.example.com:
          ansible_host: 192.168.56.13
    production:
      children:
        webservers:
        dbservers:
```

---

## A5. List inventory hosts

```bash
# 1. All hosts in webservers
ansible webservers --list-hosts

# 2. All hosts in production
ansible production --list-hosts

# 3. Count of all managed nodes
ansible all --list-hosts | tail -1
# OR
ansible all --list-hosts | grep -c 'node'
```

---

## A6. Ansible modules

1. A **module** is a reusable, standalone unit of code that Ansible runs on managed nodes to perform a specific task (install packages, manage files, control services, etc.).

2. Offline documentation:
```bash
ansible-doc ansible.builtin.user
```

3. Show examples:
```bash
ansible-doc -s ansible.builtin.user    # short form / snippet
ansible-doc ansible.builtin.user       # full docs including EXAMPLES section
```

---

## A7. Understand facts

1. **Facts** are variables automatically discovered from managed nodes by Ansible at the start of a play using the `setup` module. They describe the system's state (OS, IP, memory, disk, etc.).

2. Gather all facts for node1:
```bash
ansible node1.lab.example.com -m setup
```

3. Hostname fact key: `ansible_hostname`

4. IPv4 addresses fact key: `ansible_all_ipv4_addresses`

---

## A8. Disable fact gathering

```yaml
---
- name: Play without fact gathering
  hosts: webservers
  gather_facts: false
  tasks:
    - name: Example task
      ansible.builtin.debug:
        msg: "Facts are disabled — ansible_ variables not available"
```

**When to disable:** In large environments where fact collection adds significant overhead and the play does not need system facts; also useful for connection tests and purely local tasks.

---

## A9. Magic variables

| Variable | Purpose | Example |
|----------|---------|---------|
| `inventory_hostname` | The name of the current host as defined in inventory | `"{{ inventory_hostname }}"` → `node1.lab.example.com` |
| `ansible_host` | The actual IP/hostname used to connect | `"{{ ansible_host }}"` → `192.168.56.11` |
| `hostvars` | Dict of all facts and vars for every host in inventory | `"{{ hostvars['node1.lab.example.com']['ansible_default_ipv4']['address'] }}"` |
| `groups` | Dict of all groups and their member hostnames | `"{{ groups['webservers'] }}"` |
| `group_names` | List of groups the **current** host belongs to | `"{{ group_names }}"` → `['webservers','production']` |

---

## A10. Complete ansible.cfg

```ini
[defaults]
inventory         = ~/inventory/hosts.ini
remote_user       = ansible
host_key_checking = False
roles_path        = ~/roles
forks             = 10

[privilege_escalation]
become            = True
become_method     = sudo
become_user       = root
become_ask_pass   = False
```
