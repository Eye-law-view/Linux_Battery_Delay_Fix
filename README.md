Battery Monitor Service

This script monitors battery status using `acpi` and restarts the `upower` service automatically when it detects a change in the battery state. It is designed to fix delayed battery status updates on some Linux systems.

Prerequisites

Ensure `acpi` is installed on your system:

`sudo apt install acpi`

Installation
Step 1: Create the Shell Script
Open the script file for editing:


`sudo nano /usr/local/bin/battery-monitor.sh`
Add the following content:


```bash
#!/bin/bash

# File to store the previous state
PREVIOUS_STATE="/tmp/battery_status"

# Function to get current charging state
get_battery_status() {
    acpi -V | grep -m 1 "Battery 0" | awk '{print $3}' | tr -d ','
}

# Initialize previous state
CURRENT_STATUS=$(get_battery_status)
echo "$CURRENT_STATUS" > $PREVIOUS_STATE

while true; do
    # Get the current battery status
    CURRENT_STATUS=$(get_battery_status)
    PREVIOUS_STATUS=$(cat $PREVIOUS_STATE)

    # Check if the state has changed
    if [[ "$CURRENT_STATUS" != "$PREVIOUS_STATUS" ]]; then
        echo "$CURRENT_STATUS" > $PREVIOUS_STATE
        echo "Battery state changed to: $CURRENT_STATUS. Restarting UPower..."
        
        # Restart UPower service
        sudo systemctl restart upower.service
    fi

    # Check every 3 seconds
    sleep 3
done
```



Save and exit (Ctrl+O, Enter, Ctrl+X).

Make the script executable:


`sudo chmod +x /usr/local/bin/battery-monitor.sh`

Step 2: Create a Systemd Service
Open the service file for editing:


`sudo nano /etc/systemd/system/battery-monitor.service`

Add the following content:

```
[Unit]
Description=Battery Monitor Service
After=multi-user.target

[Service]
ExecStart=/usr/local/bin/battery-monitor.sh
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

`Save and exit (Ctrl+O, Enter, Ctrl+X).`

Step 3: Enable and Start the Service

Reload systemd to recognize the new service:
`sudo systemctl daemon-reload`

Enable the service to start at boot:
`sudo systemctl enable battery-monitor.service`

Start the service:
`sudo systemctl start battery-monitor.service`

Check the service status:
`sudo systemctl status battery-monitor.service`

If the service is not running, start it manually:
`sudo systemctl start battery-monitor.service`
Verification
To ensure the service runs at boot, reboot your system:

sudo reboot
After reboot, check the service status:

`sudo systemctl status battery-monitor.service`
