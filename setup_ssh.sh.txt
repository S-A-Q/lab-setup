#!/bin/bash

# --- Configuration ---
# Set this to the name of your public key file in the repo.
# Ensure this file contains your public key in OpenSSH single-line format!
PUBLIC_KEY_FILE="id_rsa.pub"

# Set this to your username on the lab machine if it's different from the user running the script
# If left empty, it uses the current user ($USER)
TARGET_USER="$USER"
# -------------------

echo "--- SSH Key Setup Automation ---"
echo "Starting setup for user: $TARGET_USER"

# 1. Check for public key file
if [ ! -f "$PUBLIC_KEY_FILE" ]; then
    echo "Error: Public key file '$PUBLIC_KEY_FILE' not found in the current directory."
    echo "Please ensure your public key is in this directory and named correctly."
    exit 1
fi

# Determine the home directory of the target user
USER_HOME=$(eval echo "~$TARGET_USER")

# 2. Create .ssh directory if it doesn't exist and set permissions
echo "-> Creating .ssh directory and setting permissions for $TARGET_USER..."
mkdir -p "$USER_HOME"/.ssh
if [ $? -ne 0 ]; then echo "Error: Failed to create ~/.ssh directory."; exit 1; fi
chmod 700 "$USER_HOME"/.ssh
if [ $? -ne 0 ]; then echo "Error: Failed to set permissions for ~/.ssh directory."; exit 1; fi
chown "$TARGET_USER":"$TARGET_USER" "$USER_HOME"/.ssh
if [ $? -ne 0 ]; then echo "Error: Failed to set ownership for ~/.ssh directory."; exit 1; fi
echo "   ~/.ssh directory verified."

# 3. Append public key to authorized_keys and set permissions
echo "-> Adding public key to authorized_keys..."
# Use 'cat' with 'tee -a' to write with sudo, which correctly handles ownership/permissions
# This prevents issues if the user is not root but TARGET_USER's home is not accessible without sudo
sudo bash -c "cat \"$PUBLIC_KEY_FILE\" >> \"$USER_HOME\"/.ssh/authorized_keys"
if [ $? -ne 0 ]; then echo "Error: Failed to append public key to authorized_keys."; exit 1; fi
chmod 600 "$USER_HOME"/.ssh/authorized_keys
if [ $? -ne 0 ]; then echo "Error: Failed to set permissions for authorized_keys."; exit 1; fi
chown "$TARGET_USER":"$TARGET_USER" "$USER_HOME"/.ssh/authorized_keys
if [ $? -ne 0 ]; then echo "Error: Failed to set ownership for authorized_keys."; exit 1; fi
echo "   Public key added."

# 4. (Optional) Disable PasswordAuthentication in sshd_config for enhanced security
#    WARNING: Ensure your key-based access is working before relying solely on it.
echo "-> Modifying /etc/ssh/sshd_config..."
# Disable PasswordAuthentication
sudo sed -i 's/^#\?PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config
# Ensure PubkeyAuthentication is enabled
sudo sed -i 's/^#\?PubkeyAuthentication no/PubkeyAuthentication yes/g' /etc/ssh/sshd_config
# Ensure UsePAM is enabled (required for most system auth)
sudo sed -i 's/^#\?UsePAM no/UsePAM yes/g' /etc/ssh/sshd_config
# Ensure PermitRootLogin does not allow password (e.g., 'no' or 'prohibit-password')
# We'll set it to 'prohibit-password' if 'yes' or commented.
sudo sed -i 's/^#\?PermitRootLogin yes/PermitRootLogin prohibit-password/g' /etc/ssh/sshd_config
# If PermitRootLogin is not 'prohibit-password' or 'no', explicitly set it to 'prohibit-password'
# This ensures it's set if the default was allowing passwords.
if ! grep -q "^PermitRootLogin \(prohibit-password\|no\)" /etc/ssh/sshd_config; then
  echo "PermitRootLogin prohibit-password" | sudo tee -a /etc/ssh/sshd_config > /dev/null
fi

echo "   /etc/ssh/sshd_config updated. Please review manually if needed (sudo nano /etc/ssh/sshd_config)."

# 5. Restart SSH service
echo "-> Restarting SSH service..."
sudo systemctl restart ssh
if [ $? -ne 0 ]; then echo "Error: Failed to restart SSH service."; exit 1; fi
echo "   SSH service restarted."

echo "--- Setup Complete! ---"
echo "You should now be able to connect via SSH without a password."
echo "Example: ssh -i /path/to/your/private_key_file ${TARGET_USER}@<your_public_ip>"

# Display Public IP at the very end for convenience
echo ""
echo "-----------------------------------"
echo "Your Lab Machine's Public IP is:"
curl -s ifconfig.me
echo "-----------------------------------"
echo ""
