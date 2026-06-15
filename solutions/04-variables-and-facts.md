# Solutions — Section 04: Variables and Facts

---

## A1. Variable precedence (low → high)

1. Role defaults (`roles/x/defaults/main.yml`)
2. Group vars (`group_vars/all`, `group_vars/<group>`)
3. Host vars (`host_vars/<host>`)
4. Play `vars:` block
5. Extra vars (`-e` at command line) — **highest**

---

## A2. host_vars and group_vars

```yaml
# group_vars/webservers.yml
http_port: 80
max_clients: 200
```

```yaml
# host_vars/node3.yml (use node3.lab.example.com if FQDN in inventory)
db_port: 3306
db_name: "appdb"
```

```yaml
# show_vars.yml
---
- name: Show web server vars
  hosts: webservers
  tasks:
    - name: Print web vars
      ansible.builtin.debug:
        msg: "http_port={{ http_port }}, max_clients={{ max_clients }}"

- name: Show db server vars
  hosts: dbservers
  tasks:
    - name: Print db vars
      ansible.builtin.debug:
        msg: "db_port={{ db_port }}, db_name={{ db_name }}"
```

---

## A3. Register and use output

```yaml
---
- name: Check disk usage
  hosts: managed
  tasks:
    - name: Run df -h /
      ansible.builtin.command: df -h /
      register: disk_info

    - name: Print df output
      ansible.builtin.debug:
        var: disk_info.stdout
```

---

## A4. set_fact

```yaml
    - name: Calculate free memory in GB
      ansible.builtin.set_fact:
        free_gb: "{{ (ansible_memfree_mb / 1024) | round(2) }}"
```

---

## A5. Magic variables — hostvars

```yaml
---
- name: Print all hosts IPs from control
  hosts: control
  tasks:
    - name: Print IP of every inventory host
      ansible.builtin.debug:
        msg: "{{ item }}: {{ hostvars[item]['ansible_default_ipv4']['address'] }}"
      loop: "{{ groups['all'] }}"
```

---

## A6. vars_files

```yaml
# vars/common.yml
http_port: 80
max_clients: 200
db_port: 3306
db_name: "appdb"
```

```yaml
# show_vars.yml (updated)
---
- name: Show vars from file
  hosts: all
  vars_files:
    - vars/common.yml
  tasks:
    - name: Print vars
      ansible.builtin.debug:
        msg: "http_port={{ http_port }}"
```

---

## A7. Override variable at runtime

```bash
ansible-playbook show_vars.yml -e "http_port=8080"
```

---

## A8. default filter

```jinja2
{{ custom_port | default(80) }}
```

---

## A9. Custom facts

```bash
# On managed node — fact file must be executable or .ini/.json/.fact format
mkdir -p /etc/ansible/facts.d
cat > /etc/ansible/facts.d/app.fact << 'EOF'
[app]
app_version=2.1
app_env=production
EOF
```

```yaml
# Deploy the fact file
- name: Deploy custom fact
  ansible.builtin.copy:
    src: files/app.fact
    dest: /etc/ansible/facts.d/app.fact
    mode: '0644'

# Re-gather facts after deploying
- name: Reload facts
  ansible.builtin.setup:

# Read custom fact
- name: Show custom fact
  ansible.builtin.debug:
    var: ansible_local.app.app.app_version
```

---

## A10. Facts caching

```ini
# ansible.cfg
[defaults]
gathering    = smart
fact_caching = jsonfile
fact_caching_connection = /tmp/ansible_facts_cache
fact_caching_timeout    = 86400   # 24 hours in seconds
```

Fact caching stores gathered facts on disk so subsequent runs skip the setup phase if facts are fresh. Useful in large inventories to reduce play startup time.

---
---
