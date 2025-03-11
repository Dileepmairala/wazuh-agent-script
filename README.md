# Wazuh Agent Installation Script(rockylinux)

This repository contains a Bash script for automating the installation of the Wazuh agent on CentOS/RHEL systems.

## Overview

The script handles the following tasks:
- Imports the Wazuh GPG key
- Adds the Wazuh repository
- Installs the Wazuh agent
- Configures the agent to connect to a specified Wazuh manager
- Enables and starts the agent service

## Prerequisites

- CentOS/RHEL system
- Root privileges (or sudo access)
- Network connectivity to the Wazuh repositories and manager

## Usage

1. Download the installation script:

```bash
curl -O https://raw.githubusercontent.com/yourusername/wazuh-agent-installer/main/install-wazuh-agent.sh
chmod +x install-wazuh-agent.sh
```

2. Run the script (with sudo if not root):

```bash
sudo ./install-wazuh-agent.sh
```

Alternatively, you can modify the WAZUH_MANAGER IP address in the script before running it.

## The Script

```bash
#!/bin/bash
# Define Wazuh Manager IP
WAZUH_MANAGER="10.10.5.72"

# Check if the script is run as root
if [ "$EUID" -ne 0 ]; then
  echo "Please run this script as root or use sudo."
  exit 1
fi

# Import the GPG key
sudo rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH

# Add the Wazuh repository
cat > /etc/yum.repos.d/wazuh.repo << EOF
[wazuh]
gpgcheck=1
gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
enabled=1
name=EL-\$releasever - Wazuh
baseurl=https://packages.wazuh.com/4.x/yum/
protect=1
EOF

# Install the Wazuh agent
sudo WAZUH_MANAGER="$WAZUH_MANAGER" yum install -y wazuh-agent

# Enable and start the Wazuh agent service
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent

# Print completion message
echo "Wazuh agent installation and setup completed successfully. The agent is now connected to the Wazuh manager at $WAZUH_MANAGER."
```

## Customization

To connect to a different Wazuh manager, modify the `WAZUH_MANAGER` variable at the top of the script:

```bash
WAZUH_MANAGER="your.manager.ip.address"
```

## Verification

After installation, you can verify that the agent is properly connected:

```bash
sudo systemctl status wazuh-agent
```

You can also check the agent logs:

```bash
sudo tail -f /var/ossec/logs/ossec.log
```

## Troubleshooting

Common issues and their solutions:

1. **Connection problems**: 
   - Ensure the Wazuh manager IP is correct
   - Check that the required ports are open (1514/UDP, 1515/TCP)
   - Verify the manager is properly configured to accept new agents

2. **Repository access issues**:
   - Check your internet connectivity
   - Verify that DNS resolution is working properly

3. **Permission issues**:
   - Make sure you're running the script with root privileges

## Supported Versions

This script has been tested with:
- Wazuh 4.x
- CentOS/RHEL 7 and 8

## License

This project is licensed under the MIT License - see the LICENSE file for details.
