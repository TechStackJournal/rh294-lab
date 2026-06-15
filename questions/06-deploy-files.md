# Section 06 — Deploy Files to Managed Hosts


> **Exam Objective:** Use Jinja2 templates and file modules to manage configuration files.

---

## Q1. copy vs template

Explain the difference between `ansible.builtin.copy` and `ansible.builtin.template`. When should you use each?

---

## Q2. Jinja2 template — httpd.conf

Create a Jinja2 template `templates/httpd.conf.j2` that generates an Apache config with:

- `Listen {{ http_port }}`
- `ServerName {{ inventory_hostname }}`
- `MaxClients {{ max_clients | default(150) }}`
- A comment block showing the date the file was generated using `ansible_date_time.date`

Write the task that deploys it.

---

## Q3. lineinfile

Write a task that ensures the line `net.ipv4.ip_forward = 1` exists in `/etc/sysctl.conf` on all managed nodes. If the line is absent, append it. If a line matching `net.ipv4.ip_forward` already exists with a different value, replace it.

---

## Q4. blockinfile

Write a task that inserts the following block into `/etc/hosts` on all managed nodes, with a custom marker:

```
# Lab nodes
192.168.56.10 control.lab.example.com control
192.168.56.11 node1.lab.example.com node1
192.168.56.12 node2.lab.example.com node2
192.168.56.13 node3.lab.example.com node3
```

---

## Q5. File permissions and ownership

Write tasks to:

1. Create `/var/www/html/index.html` with content `"Hello from {{ inventory_hostname }}"`, owned by `apache:apache`, mode `0644`
2. Create directory `/opt/app/data` with mode `0750`, owned by `appuser:appgroup`

---

## Q6. Fetch a file from managed nodes

Write a task that fetches `/var/log/messages` from every managed node and saves each copy locally to `logs/{{ inventory_hostname }}/messages`.

---

## Q7. Jinja2 filters

Use the following Jinja2 filters in a `debug` task (show one `debug` task per filter):

| Filter | Input | Expected Output |
|--------|-------|-----------------|
| `upper` | `"hello"` | `"HELLO"` |
| `replace` | `"foo-bar"` | `"foo_bar"` |
| `join` | `['a','b','c']` | `"a,b,c"` |
| `length` | `[1,2,3,4]` | `4` |

---

## Q8. Conditional content in templates

Extend the `httpd.conf.j2` template to include an `SSLEngine on` directive **only** when a variable `enable_ssl` is set to `true`.

---

## Q9. Loop in a template

Write a Jinja2 template `vhosts.conf.j2` that generates a `<VirtualHost>` block for each item in a list variable `vhosts`, where each item has `name` and `docroot` keys.

---

## Q10. validate directive

Add a `validate` directive to the task that deploys `httpd.conf.j2` so that `httpd -t -f %s` is run before the file is placed in production. What happens if validation fails?

---
---
