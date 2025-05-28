# MikroTik Automated Backup Script with Email Notification

This RouterOS script automates the process of creating MikroTik configuration backups and sends them to a specified email address. It generates both a textual configuration export (`.rsc`) and a password-protected binary backup file (`.backup`).

## Key Features:

* **Dual Backup Types:**
    * Creates a configuration export file (`/export file=filename.rsc`).
    * Creates a full binary backup file (`/system backup save name=filename.backup`) with AES-SHA256 encryption.
* **Email Notification:** Sends both backup files as attachments to the specified email address.
* **Informative Email Body:** The email body includes:
    * Detailed system information (device identity, board name, RouterOS version, firmware version).
    * Resource usage statistics (free HDD space, free memory, uptime).
    * Critical and error log messages from the router for the current day.
* **Timestamped Filenames:** Backup files are named using the system identity and a timestamp (YYYY-Mon-DD-HHMM) for easy identification and sorting.
* **Automatic Cleanup:** Deletes the backup files from the router after a successful email dispatch.
* **Error Handling:** Built-in logic to handle potential errors during backup creation, email sending, and file removal stages.
* **Easy Configuration:** Key parameters (email address, encryption password) are easily configurable at the beginning of the script.

## Configuration:

1.  **Adjust variables at the beginning of the script:**
    * `EmailTo`: Your email address to receive the backups.
    * `BackupEncryptionPassword`: The password for encrypting the binary backup file. **Important:** Since RouterOS v6.43, if no password is provided, the `.backup` file will be unencrypted.
2.  **Configure Email on MikroTik:** Ensure that email sending is configured on your router via `/tool e-mail`. You'll need to specify your SMTP server, port, and credentials if required. https://help.mikrotik.com/docs/spaces/ROS/pages/24805377/E-mail

## Usage:

1.  Copy the script to your MikroTik router (e.g., via WinBox, SSH, or by uploading to `Files`).
2.  The recommended way to use this script is to add it to the scheduler (`/system scheduler`) for regular automated execution (e.g., daily).

## Compatibility:

* The script is designed and tested for RouterOS v7.x.
* The password encryption feature for `.backup` files requires RouterOS v6.43 or newer.
