# 🚀 Lab Setup Automation

This guide explains how to use this repository to quickly set up **SSH key-based authentication** on new **Ubuntu lab environments**.

---

## 📋 Prerequisites

Before you start, make sure you have:

- ✅ Generated an **SSH Key Pair** on your local machine.
  _If not, follow a guide for [OpenSSH](https://www.ssh.com/academy/ssh/keygen) or use basic MobaXterm instructions._

- ✅ Extracted your **Public Key in OpenSSH Format**.
  _This should be a single-line string starting with `ssh-rsa AAAA...` or `ssh-ed25519 AAAA...`._

- ⚠️ **IMPORTANT:** Replace the contents of the `id_rsa.pub` file in this repo with **your own public key**.
  _Never include your private key in this repository._

---

## ⚙️ Setup Steps

Follow these steps **on your Ubuntu Lab Machine** and then **on your Local Machine**.

---

### 🖥️ On Your Ubuntu Lab Machine
1. **Install Git (if not already installed):**

```
   sudo apt update && sudo apt install git -y
```

1. **Clone this repository:**
    
    ```bash
    git clone https://github.com/YourUsername/lab-setup.git
    ```
    
    > 🔁 Replace YourUsername with your actual GitHub username.
    > 
2. **Navigate into the cloned directory:**
    
    ```bash
    cd lab-setup
    ```
    
3. **Make the setup script executable:**
    
    ```bash
    chmod +x setup_ssh.sh
    ```
    
4. **Run the setup script:**
    
    ```bash
    bash setup_ssh.sh
    ```
    
    > 🛠️ The script will configure SSH keys, update sshd_config, and restart the SSH service.
    > 
    > 
    > You may be prompted for your current user's password.
    > 

---

### 💻 On Your Local Machine (MobaXterm)

Once the lab machine setup is complete, follow these steps to SSH into it:

1. **Open MobaXterm.**
2. **Create a new SSH session:**
    - Click on **"Session"** (top-left).
    - Choose **"SSH"** as the session type.
3. **Configure SSH connection:**
    - **Remote Host:** Enter the public IP of your lab machine
    - **Specify Username:** Check the box and enter the username you set in `setup_ssh.sh` (e.g., `toor` or `ubuntu`).
    
    OR 
    
    ```bash
    ssh -i /path/to/your/private_key_file username@public_ip_address
    ```
    
4. **Provide your Private Key:**
    - Go to the **"Advanced SSH settings"** tab.
    - Check **"Use private key"**.
    - Click the folder icon and browse to your private key file (e.g., `C:\Users\YourUser\.ssh\my_lab_key.pem`).
5. **Connect and enjoy your secured SSH access!** 🎉
