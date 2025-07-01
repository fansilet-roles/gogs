# Managing Secrets for Molecule Testing

This role requires a vaulted password for the `molecule prepare` step to install host-level dependencies. Before running `molecule create` or `molecule test`, you must manually create two files within this `secrets/` directory. These files are intentionally ignored by `.gitignore` and should not be committed to version control.

## 1. Create the Vault Password File (`.vault`)

This file contains the password that Ansible will use to encrypt and decrypt your secrets file.

**Command:**

```bash
echo "your_chosen_password" > secrets/.vault
```

- Replace `"your_chosen_password"` with a strong password of your choice.
- Ensure there are no trailing spaces in the file.

---

## 2. Create the Encrypted Secrets File (`secret.yml`)

This file contains the actual secret variables that your playbook will use. It will be encrypted using the password from your `.vault` file. The `prepare.yml` playbook specifically looks for the `vault_pass` variable to use as the `sudo` password.

**Command:**

```bash
ansible-vault create secrets/secret.yml
```

This command will prompt you for a password. **You must enter the same password you used in the `.vault` file.**

After you enter the password, an editor will open. Add the following content to the file:

```yaml
vault_pass: "your_sudo_password"
```

- Replace `"your_sudo_password"` with your user's `sudo` password for the machine running Molecule.

Save and close the editor. The file will be created in an encrypted format. With both `.vault` and `secret.yml` in place, you can now proceed with `molecule` commands.
