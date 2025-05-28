# MikroTik Automated Backup Script (via Email)

This RouterOS script automates the creation of MikroTik configuration backups and sends them to a specified email address. It generates both a textual configuration export (`.rsc`) and an AES-SHA256 encrypted binary backup file (`.backup`).

**Tested on RouterOS version: 7.19.1**

## Key Features:

* **Dual Backup Types:**
    * Creates a configuration export file (`/export file=filename.rsc`).
    * Creates a full binary backup file (`/system backup save name=filename.backup`) with AES-SHA256 encryption.
* **Password from File:** The encryption password for the binary backup is read directly from a specified local file.
    * **Important:** The script *assumes this file exists* and uses its content *as-is* (raw, without trimming whitespace or newlines). If the file is missing, the script will terminate with an error. Any newlines or spaces in the file will become part of the password, potentially leading to issues with backup decryption.
* **Email Notification:** Sends both backup files as attachments.
* **System Information in Email:** The email body includes key system details:
    * Device identity, board name, RouterOS version, firmware version.
    * Resource usage: HDD space, memory, uptime.
* **No Logs in Email:** This version **does not** collect or include system logs in the email body to keep the email concise.
* **Timestamped Filenames:** Backup files are named using the system identity and a timestamp (YYYY-Mon-DD-HHMM) for easy identification.
* **Automatic Cleanup:** Removes backup files from the router after successful email dispatch.
* **Error Handling:** Includes `do...on-error` blocks for critical operations.

## Configuration:

1.  **Script Variables (at the top of the script):**
    * `:local EmailTo "your_email@example.com";` - Set your recipient email address.
    * `:local PasswordFileName "password_file.txt";` - Set the name of the file that stores the encryption password. This file must be present in the root of the MikroTik filesystem.
2.  **Password File:**
    * Create the specified password file (e.g., `password_file.txt`) in the root of your MikroTik's filesystem.
    * Place **only** the desired encryption password into this file. Be careful with extra spaces or newlines, as they will be treated as part of the password.
3.  **MikroTik Email Setup:**
    * Ensure that email sending is correctly configured on your router via `/tool e-mail` (SMTP server, port, credentials if required).

## Usage:

* **Important:** To prevent the `PasswordFileName` from being exposed in `.rsc` configuration exports, this script is intended to be run directly from the MikroTik Scheduler.
* Copy the **entire script source code** and paste it into the `On Event` field of a new Scheduler task (`/system scheduler`).
* Set the desired interval for the scheduler (e.g., daily).

## Notes on Encryption Password:

* The binary backup file (`.backup`) uses the password read from the specified file for AES-SHA256 encryption.
* Remember that RouterOS v6.43 and later will create an *unencrypted* backup if the password string provided to the `/system backup save` command is empty. This script logs a warning if the password file is found but its content is empty.
