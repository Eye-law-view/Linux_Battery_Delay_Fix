**UBUNTU/DEBIAN delay battery status change fix while charging and discharging by sagar parajuli
**
***************************************************************************************************************
**1. Create a Shell Script
This script will run acpi -V every 3 seconds and restart upower when it detects a status change.

Open a text editor to create the script:**
***************************************************************************************************************
sudo nano /usr/local/bin/battery-monitor.sh



***************************************************************************************************************
**Add the following content:**
***************************************************************************************************************

#!/bin/bash

# Variables to store the previous state
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



***************************************************************************************************************
**Save and exit (Ctrl+O, Enter, Ctrl+X).
**

**Make the script executable:
**
***************************************************************************************************************



sudo chmod +x /usr/local/bin/battery-monitor.sh

***************************************************************************************************************
**2. Create a Systemd Service
Create a new service file:**
***************************************************************************************************************

sudo nano /etc/systemd/system/battery-monitor.service


***************************************************************************************************************
**Add the following content:
**
***************************************************************************************************************


[Unit]
Description=Battery Monitor Service
After=multi-user.target

[Service]
ExecStart=/usr/local/bin/battery-monitor.sh
Restart=always
User=root

[Install]
WantedBy=multi-user.target




***************************************************************************************************************
**Save and exit (Ctrl+O, Enter, Ctrl+X).
**
***************************************************************************************************************

**3. Enable and Start the Service
**
***************************************************************************************************************

Reload systemd to recognize the new service:

sudo systemctl daemon-reload



***************************************************************************************************************
**Enable the service to start at boot:
**
***************************************************************************************************************

sudo systemctl enable battery-monitor.service



***************************************************************************************************************
**Start the service:
**
***************************************************************************************************************



sudo systemctl start battery-monitor.service


***************************************************************************************************************
**4. Verify the Service
To ensure the service is running and functioning correctly:

Check the status of the service:**

***************************************************************************************************************

sudo systemctl status battery-monitor.service 
***************************************************************************************************************
**If not started:**
sudo systemctl start battery-monitor.service

***************************************************************************************************************
The service will now monitor the battery status every 3 seconds and restart upower automatically when a change is detected.
 

 

 


