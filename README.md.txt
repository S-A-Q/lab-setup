# Lab Setup Automation

This repository contains a script to quickly set up SSH key-based authentication on new Ubuntu lab environments.

## Setup

1.  **Generate SSH Key Pair:** If you haven't already, generate an SSH key pair on your local machine. Ensure you extract the public key in OpenSSH format (single line starting with `ssh-rsa AAAA...` or `ssh-ed25519 AAAA...`).
2.  **Add your Public Key:** Replace the content of `id_rsa.pub` in this repository with your own public key.
3.  **Clone this repository** onto your new Ubuntu lab machine (e.g., via Guacamole access).
    ```bash
    git clone [https://github.com/YourUsername/lab-setup.git](https://github.com/YourUsername/lab-setup.git)
    cd lab-setup
    ```
4.  **Run the setup script:**
    ```bash
    bash setup_ssh.sh
    ```

## What the Script Does

The `setup_ssh.sh` script performs the following actions:
- Creates the `.ssh` directory in your home folder if it doesn't exist.
- Sets appropriate permissions for `.ssh` (700).
- Appends your public key from `id_rsa.pub` to `~/.ssh/authorized_keys`.
- Sets appropriate permissions for `authorized_keys` (600).
- **Optionally:** Disables password authentication in `/etc/ssh/sshd_config` for enhanced security.
- Restarts the SSH service to apply changes.