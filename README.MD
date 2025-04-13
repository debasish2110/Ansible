# Ansible Notes

## What is Ansible?

Ansible is an open-source, command-line IT automation software written in Python. It can configure systems, deploy software, and orchestrate advanced workflows to support application deployment, system updates, and more. Ansible's strengths lie in its simplicity and ease of use.

---

## How Ansible Works

Ansible consists of:

- **Control Node**: The machine where Ansible is installed and run.
- **Managed Nodes**: The target machines managed by the control node.

### Install Ansible (on RHEL-based systems)

```bash
sudo yum update -y
sudo yum install ansible -y
```

### SSH Connection

1. Generate SSH keys:
   ```bash
   ssh-keygen
   ```
2. Copy public key to target server:
   ```bash
   ssh-copy-id user@<target-ip>
   ```
3. Or manually copy the contents of `~/.ssh/id_rsa.pub` to the target server's `~/.ssh/authorized_keys`.

This allows the Ansible server to connect and ping the target.

---

## Inventory File

### What is an Inventory File?

Defines managed nodes and allows grouping for bulk task execution.

- Custom inventory file:
  ```bash
  vi inventory
  ```
- Default inventory path:
  ```bash
  vi /etc/ansible/hosts
  ```

**Example**:

```ini
[web]
192.168.1.2
[test]
192.168.1.3
```

---

## Ad-hoc Commands

Quick one-liner commands for immediate execution.

```bash
ansible -i inventory all -a "yum install git -y" -b
ansible all -a "yum install git -y" -b  # Uses default inventory
ansible web -a "yum install git -y" -b  # For specific group
```

- `-a`: Ad-hoc argument
- `-b`: Become root
- `all`: All hosts

**More Examples**:

```bash
ansible -i inventory all -a "yum install maven -y" -b
ansible all -a "git --version" -b
ansible -i inventory all -a "touch file100" -b
```

---

## Ansible Modules

Modules are reusable units of work in Ansible.

### Common Modules

- **ping**:

  ```bash
  ansible -i inventory all -m ping
  ```

- **stat**:

  ```bash
  ansible -i inventory all -m stat -a "path=/var/www/html"
  ```

- **user**:

  ```bash
  ansible -i inventory all -m user -a "name=naresh" -b
  ```

- **setup**:

  ```bash
  ansible -i inventory all -m setup
  ```

- **file (touch file)**:

  ```bash
  ansible -i inventory all -m file -a "name=demo state=touch"
  ```

- **copy**:

  ```bash
  ansible -i inventory all -m copy -a "src=file1 dest=~"
  ```

- **yum or apt**:

  ```bash
  ansible all -m yum -a "name=httpd state=latest" -b
  ansible all -m yum -a "name=httpd state=present" -b
  ansible all -m yum -a "name=httpd state=absent" -b
  ```

- **service**:

  ```bash
  ansible all -m service -a "name=httpd state=started" -b
  ansible all -m service -a "name=httpd state=stopped" -b
  ansible all -m service -a "name=httpd state=restarted" -b
  ```

---

## Disable Host Key Checking

Edit configuration file:

```bash
sudo vi /etc/ansible/ansible.cfg
```

Add:

```ini
[defaults]
host_key_checking = False
```

---

## Ansible Playbooks

Playbooks are YAML files that define multiple tasks.

### Basic Playbook

```yaml
---
- name: first playbook
  hosts: all
  become: true
  tasks:
    - name: install httpd software
      yum:
        name: httpd
        state: latest

    - name: start web server
      service:
        name: httpd
        state: started
```

### Run Playbook

```bash
ansible-playbook -i inventory test-playbook.yaml
```

---

## Deploy Through Copy

```yaml
---
- name: deploy httpd and copy index.html
  hosts: all
  become: true
  tasks:
    - name: install httpd software
      yum:
        name: httpd
        state: latest

    - name: start web server
      service:
        name: httpd
        state: started

    - name: copying the files
      copy:
        src: index.html
        dest: /var/www/html/index.html

    - name: restart server
      service:
        name: httpd
        state: restarted
```

---

## Global Variables Example

```yaml
---
- name: playbook with global vars
  hosts: all
  become: true
  vars:
    a: httpd
    b: present
    c: started
    d: restarted

  tasks:
    - name: install httpd software
      yum:
        name: "{{ a }}"
        state: "{{ b }}"

    - name: start web server
      service:
        name: "{{ a }}"
        state: "{{ c }}"

    - name: copying the files
      copy:
        src: index.html
        dest: /var/www/html/index.html

    - name: restart server
      service:
        name: "{{ a }}"
        state: "{{ d }}"
```

---

## Tags in Ansible

Tags help you execute or skip specific tasks.

### Tagged Playbook Example

```yaml
- name: playbook with tags
  hosts: all
  tasks:
    - name: installing git
      yum:
        name: git
        state: present
      tags: a

    - name: installing maven
      yum:
        name: maven
        state: present
      tags: b

    - name: create user
      user:
        name: test
        state: present
      tags: c
```

### Tagged Execution Examples

```bash
ansible-playbook name.yml --tags a
ansible-playbook name.yml --tags b,c
ansible-playbook name.yml --skip-tags "c"
ansible-playbook name.yml --skip-tags "c,d"
```