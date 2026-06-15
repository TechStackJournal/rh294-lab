# Solutions — Section 06: Deploy Files

---

## A1. copy vs template

| | `copy` | `template` |
|-|--------|-----------|
| Source | Static file | Jinja2 `.j2` file |
| Variable substitution | No | Yes |
| Use when | Deploying binary files, static configs | Configs that vary per host/group |

---

## A2. Jinja2 template

```jinja2
{# templates/httpd.conf.j2 #}
# Managed by Ansible — generated {{ ansible_date_time.date }}

Listen {{ http_port }}
ServerName {{ inventory_hostname }}
MaxClients {{ max_clients | default(150) }}
```

```yaml
- name: Deploy httpd.conf
  ansible.builtin.template:
    src: templates/httpd.conf.j2
    dest: /etc/httpd/conf/httpd.conf
    owner: root
    group: root
    mode: '0644'
  notify: Restart httpd
```

---

## A3. lineinfile

```yaml
- name: Enable IP forwarding
  ansible.builtin.lineinfile:
    path: /etc/sysctl.conf
    regexp: '^net\.ipv4\.ip_forward'
    line: 'net.ipv4.ip_forward = 1'
    state: present
```

---

## A4. blockinfile

```yaml
- name: Add lab hosts to /etc/hosts
  ansible.builtin.blockinfile:
    path: /etc/hosts
    marker: "# {mark} ANSIBLE MANAGED LAB HOSTS"
    block: |
      # Lab nodes
      192.168.56.10 control.lab.example.com control
      192.168.56.11 node1.lab.example.com node1
      192.168.56.12 node2.lab.example.com node2
      192.168.56.13 node3.lab.example.com node3
```

---

## A5. File permissions and ownership

```yaml
- name: Deploy index.html per host
  ansible.builtin.copy:
    content: "Hello from {{ inventory_hostname }}"
    dest: /var/www/html/index.html
    owner: apache
    group: apache
    mode: '0644'

- name: Create app data directory
  ansible.builtin.file:
    path: /opt/app/data
    state: directory
    owner: appuser
    group: appgroup
    mode: '0750'
```

---

## A6. Fetch files

```yaml
- name: Fetch /var/log/messages from all managed nodes
  ansible.builtin.fetch:
    src: /var/log/messages
    dest: "logs/{{ inventory_hostname }}/messages"
    flat: true
```

---

## A7. Jinja2 filters

```yaml
- name: upper filter
  ansible.builtin.debug:
    msg: "{{ 'hello' | upper }}"

- name: replace filter
  ansible.builtin.debug:
    msg: "{{ 'foo-bar' | replace('-', '_') }}"

- name: join filter
  ansible.builtin.debug:
    msg: "{{ ['a','b','c'] | join(',') }}"

- name: length filter
  ansible.builtin.debug:
    msg: "{{ [1,2,3,4] | length }}"
```

---

## A8. Conditional SSL in template

```jinja2
Listen {{ http_port }}
ServerName {{ inventory_hostname }}

{% if enable_ssl | default(false) %}
SSLEngine on
SSLCertificateFile /etc/ssl/certs/server.crt
SSLCertificateKeyFile /etc/ssl/private/server.key
{% endif %}
```

---

## A9. Loop in template

```jinja2
{# templates/vhosts.conf.j2 #}
{% for vhost in vhosts %}
<VirtualHost *:{{ http_port }}>
    ServerName {{ vhost.name }}
    DocumentRoot {{ vhost.docroot }}
</VirtualHost>

{% endfor %}
```

---

## A10. validate directive

```yaml
- name: Deploy httpd.conf with validation
  ansible.builtin.template:
    src: templates/httpd.conf.j2
    dest: /etc/httpd/conf/httpd.conf
    validate: "httpd -t -f %s"
```

`%s` is replaced with a temporary copy of the file. If `httpd -t` exits non-zero (syntax error), Ansible **aborts the task without replacing the production file** — the old config remains intact.

---
---
