
# 🔐 Ansible Vault: In-Depth Guide

Ansible Vault is a feature of Ansible that allows you to securely store sensitive data such as passwords, API keys, certificates, and other confidential information in encrypted files. It is especially useful when storing sensitive information in version control systems like Git.

---

## 🛠️ Common Use Cases

- Encrypting inventory files that contain host credentials
- Protecting variables such as API keys, database passwords, etc.
- Securing secrets in `group_vars/` or `host_vars/` directories
- Encrypting private keys or certs used in deployments

---

## 📁 File-Level Encryption

You can encrypt whole files (YAML, plaintext, etc.):

```bash
ansible-vault encrypt secrets.yml
```

Once encrypted, the file starts with:

```yaml
$ANSIBLE_VAULT;1.1;AES256
```

To decrypt:

```bash
ansible-vault decrypt secrets.yml
```

To view without decrypting:

```bash
ansible-vault view secrets.yml
```

To edit in-place securely:

```bash
ansible-vault edit secrets.yml
```

---

## 🔁 Re-keying

Change the encryption password:

```bash
ansible-vault rekey secrets.yml
```

---

## 🔄 Multiple Files at Once

Encrypt/decrypt/edit/view multiple files:

```bash
ansible-vault encrypt vars1.yml vars2.yml
```

---

## 🔑 Using Vault in Playbooks

If you have encrypted variables (e.g. in `group_vars/all/vault.yml`):

```yaml
# group_vars/all/vault.yml
vault_db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          6238616530613762363439316238...
```

Then in your playbook:

```yaml
vars:
  db_password: "{{ vault_db_password }}"
```

You need to provide the vault password when running the playbook.

---

## 🏃 Running with Vault

Use `--ask-vault-pass` to prompt for password:

```bash
ansible-playbook site.yml --ask-vault-pass
```

Or use a password file:

```bash
ansible-playbook site.yml --vault-password-file ~/.vault_pass.txt
```

---

## 🔧 Vault IDs (for Multiple Vaults)

You can have different vaults for different environments (e.g. `prod`, `dev`):

```bash
ansible-vault encrypt --vault-id dev@prompt dev_vars.yml
ansible-vault encrypt --vault-id prod@prompt prod_vars.yml
```

Then run:

```bash
ansible-playbook site.yml --vault-id dev@prompt
```

Or mix them:

```bash
ansible-playbook site.yml   --vault-id dev@~/.dev_pass.txt   --vault-id prod@~/.prod_pass.txt
```

---

## ✅ Best Practices

- Avoid encrypting entire playbooks — just encrypt sensitive variables/files.
- Use vault IDs to manage secrets across environments.
- Store vault passwords securely, never hard-code or commit them.
- Use Ansible roles and separate `vars`, `defaults`, and `vaults` to keep things modular.

---

## ⚠️ Limitations

- Not a full secret management system (e.g., no dynamic secrets).
- Only supports file-based encryption, not field-level (unless manually).
- Vault password management can become challenging at scale (use external tools).

---

## 🔐 Alternatives / Integrations

For large-scale setups, consider integrating Ansible with:

- [HashiCorp Vault](https://www.vaultproject.io/)
- [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/)
- [Azure Key Vault](https://azure.microsoft.com/en-us/products/key-vault/)
- Ansible Collections that support dynamic secret fetching

---

# Ansible Vault: Running Encrypted Files Without a Password Prompt

Ansible Vault allows you to securely encrypt files and variables, but sometimes you'd like to run a playbook without having to manually enter the password each time. This guide explains how to run Ansible Vault encrypted files **without a password prompt**.

---

## 1. Using a Vault Password File

You can store the vault password in a **password file** and reference this file when running the playbook. This way, the playbook can be executed automatically without prompting for the password.

### Steps:

1. **Create a Vault Password File**:
   - Create a file (e.g., `~/.vault_pass.txt`) that contains your vault password.

2. **Run the Playbook with the Password File**:
   ```bash
   ansible-playbook site.yml --vault-password-file ~/.vault_pass.txt
   ```

This allows you to run the playbook without needing to manually input the password each time.

---

## 2. Using an Environment Variable for the Vault Password

Alternatively, you can store the vault password in an **environment variable** and reference it when running the playbook.

### Steps:

1. **Set the Environment Variable**:
   ```bash
   export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass.txt
   ```

2. **Run the Playbook**:
   ```bash
   ansible-playbook site.yml
   ```

The playbook will automatically use the vault password stored in the environment variable and run without prompting for the password.

---

## 3. Automatic Decryption with Ansible Tower / AWX

If you're using **Ansible Tower** (or its open-source version **AWX**), you can securely store the vault password as a **credential** within the Tower interface, which can be used automatically when running a playbook, eliminating the need for a password file or manual entry.

---

## Important Considerations

- **Security**: Storing the vault password in plain text (e.g., in a file or environment variable) can be a security risk if not properly secured. Ensure that the vault password file is **readable only by the Ansible user** and that the environment variable is not exposed to unauthorized users.
  
- **Vault Password Management**: If managing multiple environments (like `dev`, `prod`, etc.), you should maintain different vault password files for each environment.

---

With these approaches, you can run Ansible Vault encrypted playbooks without manually entering the password each time.
