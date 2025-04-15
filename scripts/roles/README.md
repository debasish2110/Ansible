# Ansible Role: my_role

## Overview

This role provides a structured, reusable, and scalable way to automate tasks using Ansible. Roles encapsulate tasks, handlers, templates, variables, and more into a standardized format that can be shared and reused.

---

## 📁 Role Directory Structure

```bash
my_role/
├── defaults/
│   └── main.yml            # Default (low precedence) variables
├── files/
│   └── <static files>      # Files to copy as-is
├── handlers/
│   └── main.yml            # Handlers triggered by notify
├── meta/
│   └── main.yml            # Role metadata and dependencies
├── tasks/
│   └── main.yml            # Main task list
├── templates/
│   └── <.j2 templates>     # Jinja2 dynamic templates
├── vars/
│   └── main.yml            # Higher precedence variables
├── tests/
│   ├── inventory           # Test inventory
│   └── test.yml            # Test playbook
├── README.md               # Documentation
```



