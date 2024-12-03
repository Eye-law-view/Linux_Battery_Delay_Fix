# Battery Monitor Service

This script monitors battery status using `acpi` and restarts the `upower` service automatically when it detects a change in the battery state. It is designed to fix delayed battery status updates on some Linux systems.

## Prerequisites

Ensure `acpi` is installed on your system:
```bash
sudo apt install acpi

Installation
Step 1: Create the Shell Script
Open the script file for editing:

bash
Copy code
sudo nano /usr/local/bin/battery-monitor.sh
Add the following content:

bash
Copy code
#!/bin/bash

PREVIOUS_STATUS=""

while true; do
    # Get the battery status using acpi
    CURRENT_STATUS=$(acpi -V | grep -m 1 "Battery 0")

    # Check if the status has changed
    if [[ "$CURRENT_STATUS" != "$PREVIOUS_STATUS" ]]; then
        # Update the previous status
        PREVIOUS_STATUS="$CURRENT_STATUS"

        # Restart UPower to fix the issue
        echo "Status changed: $CURRENT_STATUS. Restarting UPower..."
        sudo systemctl restart upower.service
    fi

    # Wait for 3 seconds before checking again
    sleep 3
done
Save and exit (Ctrl+O, Enter, Ctrl+X).

Make the script executable:

bash
Copy code
sudo chmod +x /usr/local/bin/battery-monitor.sh
Step 2: Create a Systemd Service
Open the service file for editing:

bash
Copy code
sudo nano /etc/systemd/system/battery-monitor.service
Add the following content:

ini
Copy code
[Unit]
Description=Battery Monitor Service
After=multi-user.target

[Service]
ExecStart=/usr/local/bin/battery-monitor.sh
Restart=always
User=root

[Install]
WantedBy=multi-user.target
Save and exit (Ctrl+O, Enter, Ctrl+X).

Step 3: Enable and Start the Service
Reload systemd to recognize the new service:

bash
Copy code
sudo systemctl daemon-reload
Enable the service to start at boot:

bash
Copy code
sudo systemctl enable battery-monitor.service
Start the service:

bash
Copy code
sudo systemctl start battery-monitor.service
Check the service status:

bash
Copy code
sudo systemctl status battery-monitor.service
If the service is not running, start it manually:

bash
Copy code
sudo systemctl start battery-monitor.service
Verification
To ensure the service runs at boot, reboot your system:
bash
Copy code
sudo reboot
After reboot, check the service status:
bash
Copy code
sudo systemctl status battery-monitor.service
