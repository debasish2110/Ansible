# Ansible Commands Cheat Sheet

This README contains a comprehensive list of commonly used Ansible CLI commands for everyday automation tasks.

---

## 🔧 Ad-Hoc Commands

Run simple Ansible tasks without a playbook.

```bash
ansible all -m ping                         # Ping all hosts
ansible webservers -m shell -a 'uptime'    # Run shell command on webservers
ansible localhost -m setup                 # Gather facts
ansible all -m command -a 'ls /var/log'    # List files in directory
ansible all -m copy -a "src=file.txt dest=/tmp/file.txt"   # Copy file
ansible all -m file -a "path=/tmp/testfile state=touch"    # Create empty file
```

---

## 📘 Playbook Commands

```bash
ansible-playbook site.yml                                # Run playbook
ansible-playbook -i inventory.ini site.yml               # Run with specific inventory
ansible-playbook playbook.yml --check                    # Dry run
ansible-playbook playbook.yml --diff                     # Show changes
ansible-playbook playbook.yml -e "var=value"             # Pass extra variables
ansible-playbook playbook.yml --start-at-task="Task Name" # Start from a specific task
ansible-playbook -l webservers playbook.yml              # Limit to specific group/host
ansible-playbook name.yml --tags a                       # runs tasks with tag name a
ansible-playbook name.yml --tags b,c                     # runs tasks with tag names b and c
ansible-playbook name.yml --skip-tags "c"                # run all tasks except the tasks with tag c
ansible-playbook name.yml --skip-tags "c,d"       # run all tasks except the tasks with tags c and d
```

---

## 🧪 Testing and Debugging

```bash
ansible-playbook playbook.yml -vvv               # Increase verbosity (can be -v, -vv, -vvv, -vvvv)
ansible-playbook playbook.yml --step             # Step through each task
ansible-inventory --list -i inventory.ini        # List all hosts in inventory
ansible-config dump                              # Show current configuration
ansible-config list                              # List available config options
ansible -m debug -a "msg='Hello World'" all      # Debug output
```

---

## 🗂 Inventory Management

```bash
ansible-inventory --list -i inventory.ini              # Show inventory in JSON format
ansible-inventory --graph -i inventory.ini             # Visualize inventory graph
ansible all -i inventory.ini -m ping                   # Ping hosts in custom inventory
```

---

## 🧹 Galaxy Roles and Collections

```bash
ansible-galaxy init myrole                             # Create a new role
ansible-galaxy install geerlingguy.apache              # Install role from Galaxy
ansible-galaxy install -r requirements.yml             # Install roles from requirements file
ansible-galaxy list                                    # List installed roles
ansible-galaxy collection install community.general    # Install collection
```

---

## 📦 Vault Commands

```bash
ansible-vault create secret.yml                # Create encrypted file
ansible-vault edit secret.yml                  # Edit encrypted file
ansible-vault view secret.yml                  # View contents
ansible-vault encrypt existing.yml             # Encrypt existing file
ansible-vault decrypt secret.yml               # Decrypt file
ansible-vault rekey secret.yml                 # Change password
```

---

## 🧠 Miscellaneous

```bash
ansible-doc -l                                 # List all available modules
ansible-doc ping                               # Show documentation for a module
ansible-pull -U <repo_url> playbook.yml        # Pull mode (pulls from Git)
ansible-playbook --syntax-check playbook.yml   # Check syntax
```

---

## 📂 Example Inventory File (INI Format)

```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[db]
db1 ansible_host=192.168.1.20
```

---

## 🔐 Example Vault Command with Password Prompt

```bash
ansible-playbook site.yml --ask-vault-pass
```

---

## 🙌 Contribution

Feel free to raise a PR to add any additional commands, modules, or tips!

