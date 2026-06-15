# Solutions — Section 05: Task Control

---

## A1. with_items → loop

```yaml
- name: Install packages
  ansible.builtin.dnf:
    name: "{{ item }}"
    state: present
  loop:
    - httpd
    - vim
    - git
```

---

## A2. Loop with dictionaries

```yaml
- name: Create users
  ansible.builtin.user:
    name: "{{ item.username }}"
    uid: "{{ item.uid }}"
    groups: "{{ item.group }}"
    append: true
  loop:
    - { username: alice, uid: 2001, group: dev }
    - { username: bob,   uid: 2002, group: dev }
    - { username: carol, uid: 2003, group: ops }
```

---

## A3. Conditionals — when

```yaml
- name: Install httpd on RHEL/AlmaLinux only
  ansible.builtin.dnf:
    name: httpd
    state: present
  when: ansible_os_family == "RedHat"
```

---

## A4. Combining conditions

```yaml
- name: Run only when RedHat and has enough memory
  ansible.builtin.debug:
    msg: "Conditions met"
  when:
    - ansible_os_family == "RedHat"
    - ansible_memfree_mb > 512
```

---

## A5. block / rescue / always

```yaml
---
- name: Block rescue example
  hosts: webservers
  become: true
  tasks:
    - name: Install nginx or fallback to httpd
      block:
        - name: Install nginx
          ansible.builtin.dnf:
            name: nginx
            state: present

        - name: Start nginx
          ansible.builtin.service:
            name: nginx
            state: started
            enabled: true

      rescue:
        - name: Log failure
          ansible.builtin.debug:
            msg: "nginx install failed, falling back"

        - name: Install httpd instead
          ansible.builtin.dnf:
            name: httpd
            state: present

      always:
        - name: Always notify completion
          ansible.builtin.debug:
            msg: "Task block complete"
```

---

## A6. Notify multiple handlers

```yaml
---
- name: Notify multiple handlers
  hosts: webservers
  become: true

  handlers:
    - name: Restart httpd
      ansible.builtin.service:
        name: httpd
        state: restarted

    - name: Reload firewalld
      ansible.builtin.service:
        name: firewalld
        state: reloaded

  tasks:
    - name: Deploy httpd config
      ansible.builtin.template:
        src: templates/httpd.conf.j2
        dest: /etc/httpd/conf/httpd.conf
      notify:
        - Restart httpd
        - Reload firewalld
```

---

## A7. Tags — commands

```bash
# 1. Run only install tasks
ansible-playbook site.yml --tags install

# 2. Skip test tasks
ansible-playbook site.yml --skip-tags test

# 3. List all tags (dry run)
ansible-playbook site.yml --list-tags
```

---

## A8. failed_when

```yaml
- name: Check for errors in log
  ansible.builtin.command: grep "ERROR" /var/log/app.log
  register: grep_result
  failed_when: grep_result.rc == 0   # fail if ERROR was found
  ignore_errors: true                # don't abort play on failure
```

---

## A9. Retries and delay

```yaml
- name: Wait for port 80 to open on node1
  ansible.builtin.wait_for:
    host: node1.lab.example.com
    port: 80
    timeout: 60
  retries: 10
  delay: 5
```

---

## A10. loop_var and label

```yaml
- name: Deploy config files
  ansible.builtin.template:
    src: "templates/{{ config_file.name }}.j2"
    dest: "/etc/app/{{ config_file.name }}.conf"
  loop: "{{ config_files }}"
  loop_control:
    loop_var: config_file      # rename 'item' to 'config_file'
    label: "{{ config_file.name }}"   # show only filename in output
```

---
---
