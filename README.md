# Battery Monitor Service

This script monitors battery status using `acpi` and restarts the `upower` service automatically when it detects a change in the battery state. It is designed to fix delayed battery status updates on some Linux systems.

## Prerequisites

Ensure `acpi` is installed on your system:
```bash
sudo apt install acpi
