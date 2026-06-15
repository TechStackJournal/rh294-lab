# Solutions — Section 08: Simplify Playbooks with Roles

---

## A1. Role directory structure

```
roles/webserver/
├── defaults/
│   └── main.yml       # Default variables (lowest precedence)
├── vars/
│   └── main.yml       # Role variables (higher precedence than defaults)
├── tasks/
│   └── main.yml       # Main task list
├── handlers/
│   └── main.yml       # Handlers
├── templates/
│   └── *.j2           # Jinja2 templates
├── files/
│   └── *              # Static files for copy/script
├── meta/
│   └── main.yml       # Role metadata, dependencies, Galaxy info
└── README.md
```

---

## A2. Create a role

```bash
ansible-galaxy role init ~/roles/webserver
```

```yaml
# roles/webserver/defaults/main.yml
http_port: 80
```

```yaml
# roles/webserver/tasks/main.yml
---
- name: Install httpd
  ansible.builtin.dnf:
    name: httpd
    state: present

- name: Deploy index.html
  ansible.builtin.template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: apache
    group: apache
    mode: '0644'

- name: Start and enable httpd
  ansible.builtin.service:
    name: httpd
    state: started
    enabled: true
```

---

## A3. Use a role in a playbook

```yaml
---
- name: Deploy web servers
  hosts: webservers
  become: true
  roles:
    - role: webserver
      vars:
        http_port: 8080
```

---

## A4. Role dependencies

```yaml
# roles/webserver/meta/main.yml
---
dependencies:
  - role: firewall
```

This ensures `firewall` runs before `webserver` whenever `webserver` is applied.

---

## A5. requirements.yml — Galaxy + GitHub

```yaml
# requirements.yml
---
roles:
  - name: geerlingguy.haproxy
    src: geerlingguy.haproxy

  - name: nginx-role
    src: https://github.com/example/nginx-role
```

```bash
ansible-galaxy role install -r requirements.yml
```

---

## A6. Install a collection

```yaml
# requirements.yml
---
collections:
  - name: ansible.posix
    version: ">=1.4.0"
```

```bash
ansible-galaxy collection install -r requirements.yml
ansible-galaxy collection list ansible.posix   # verify
```

---
---
