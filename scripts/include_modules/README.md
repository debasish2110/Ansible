
# Ansible Include Modules Guide

This document explains how to use different types of **include modules** in Ansible to modularize your playbooks and make them more maintainable.

---

## 🔹 Types of Include Modules in Ansible

| Module             | Description                                                     |
|--------------------|-----------------------------------------------------------------|
| `include_tasks`     | Includes a file containing **tasks** dynamically at runtime     |
| `import_tasks`      | Includes a file containing **tasks** statically at parse time   |
| `include_vars`      | Includes **variables** from a file                              |
| `import_playbook`   | Includes another **playbook** into your main playbook           |

---

## 📘 1. Using `include_tasks`

Use `include_tasks` to include task files dynamically. This is useful when using conditions.

### Example

**install_httpd.yml**
```yaml
- name: Install httpd
  yum:
    name: httpd
    state: present

- name: Start httpd
  service:
    name: httpd
    state: started
```

**main.yml**
```yaml
- name: Web Server Setup
  hosts: all
  become: true

  tasks:
    - name: Include the httpd tasks
      include_tasks: install_httpd.yml
```

---

## 📘 2. Using `import_tasks`

Use `import_tasks` for static task inclusion — evaluated at **parse time** (before runtime).

```yaml
- name: Include tasks statically
  import_tasks: install_httpd.yml
```

---

## 🔄 `include_tasks` vs `import_tasks`

| Feature        | `include_tasks`             | `import_tasks`          |
|----------------|-----------------------------|--------------------------|
| When condition | ✅ Supported                 | ✅ Supported              |
| Dynamic        | ✅ Yes (runtime)             | ❌ No (parse-time only)  |
| Use case       | Conditional or dynamic includes | Static, known includes |

---

## 📘 3. Using `include_vars`

To load variables from a file:

**vars.yml**
```yaml
http_port: 80
```

**main.yml**
```yaml
- name: Load variables from file
  include_vars: vars.yml
```

---

## 📘 4. Using `import_playbook`

Use this when you want to include an entire playbook inside another.

**main_playbook.yml**
```yaml
- import_playbook: webserver_setup.yml
```

---

## ✅ Best Practices

- Use `import_tasks` when the file should **always** be included.
- Use `include_tasks` when inclusion depends on **conditions**.
- Use `include_vars` for dynamic variable loading.
- Use `import_playbook` to split your automation into multiple playbooks.

---

## execution in ec2 machine

![include modules execution](../../include_modules.png)

Happy Automating! 🚀
