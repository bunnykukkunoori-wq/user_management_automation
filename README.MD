 # User Management Automation (SysOps Challenge)

This project provides an automated way to create and manage Linux user accounts using a bash script.

It is designed for SysOps, DevOps, and IT administrators who need to onboard multiple users quickly and securely.

✪ # The script reads a user input file and automatically:

✦ Creates users and required groups

✦ Sets secure passwords

✦ Configures home directories

✦ Logs all actions

✦ Generates documentation-ready sample log output

# Features

1.Automated User Creation:

Reads entries from an input file in the format:

username;group1,group2,group3

2.Group Management:

Creates missing groups

Adds users to secondary groups

Handles existing users gracefully

3.Secure Password Generation:

Creates a random 12-character password for new users

Saves credentials securely in /var/secure/user_passwords.txt with chmod 600

4.Home Directory Management:

Ensures /home/username exists

Applies secure permissions (700)

Assigns correct ownership

5.Logging:

Logs all actions (success, errors, skips) to:

/var/log/user_management.log

Automatically copies the log to the project folder for documentation:

sample_output/user_management.log.sample

6.Fully Commented Script:

Every major function and command is documented inside the script.

# Project Structure

```
users management/
├── create_users.sh
├── employees.txt
├── passwords.txt
├── user_management.log
└── sample_output/
    └── user_management.log.sample
```



# Files Explained

| File                                      | Purpose                                  |
|-------------------------------------------|------------------------------------------|
| create_users.sh                           | Main automation script                   |
| employees.txt                             | Input file containing usernames & groups |
| passwords.txt                             | Generated passwords for new users        |
| user_management.log                       | Full activity log                        |
| sample_output/user_management.log.sample  | Log copy for documentation               |

# Input File Format (employees.txt)

Each non-comment line contains:

username;group1,group2,group3

Example:

# Dev Team Users

light; sudo,dev,www-data

siyoni; sudo

manoj; dev,www-data

# Rules:

Whitespace is ignored

Lines beginning with # are ignored

Groups are comma-separated

Groups may be empty (allowed)

# How the Script Works (Step-by-Step)

● Pre-checks:

Must be run as root

Input file must exist

Creates secure files:

/var/secure/user_passwords.txt (600)

/var/log/user_management.log (600)

● Reads Each Line:

Removes whitespace

Skips comments and empty lines

Extracts username and groups

● Group Handling:

Ensures all listed groups exist

Creates missing groups automatically

● User Handling:

When the user does not exist:

Creates the user

Adds them to groups

Creates /home/username

Sets owner + permissions

Generates a strong password

Stores credentials securely

When the user already exists:

Does not reset password

Ensures group membership

Corrects home directory permissions

Logs the action

● Logging:

Every action is logged with timestamps:

/var/log/user_management.log


A copy of the exact log is exported to:

sample_output/user_management.log.sample

# How to Run the Script

1.Make script executable

chmod +x create_users.sh

2.Run with sudo

sudo ./create_users.sh employees.txt

# Design View

# A) Input Layer

Reads the employees.txt file

Removes whitespace

Skips comments

Extracts:

username

group list

# B) Processing Layer

Handles all system operations:

| Component                 | Responsibility                               |
|---------------------------|-----------------------------------------------|
| User Validator            | Checks if user exists already                |
| Group Manager             | Creates missing groups                       |
| User Creator              | Creates new system user accounts             |
| Group Assigner            | Adds user to all specified groups            |
| Home Directory Manager    | Ensures `/home/username` exists              |
| Permission Fixer          | Sets secure permissions (`700`)              |
| Password Generator        | Generates 12-character password              |
| Password Handler          | Stores password safely (WSL compliant)       |
| Logger                    | Records all actions with timestamp           |

# C) Output Layer

Creates 3 output files:

1.passwords.txt

Stores generated passwords securely.

2.user_management.log

Full detailed log with timestamps.

3.sample_output/user_management.log.sample

A clean copy for documentation or submission.


# Sample Log Output

2025-11-12 16:15:22 - ===== Starting user provisioning from 'employees.txt' =====

2025-11-12 16:15:22 - Processing user: light (groups: sudo,dev,www-data)

2025-11-12 16:15:22 - Created group: dev

2025-11-12 16:15:22 - SUCCESS: Created user 'light' with supplementary groups: sudo,dev,www-data

2025-11-12 16:15:22 - Set ownership and permissions for /home/light

2025-11-12 16:15:22 - Stored credentials for 'light' in /var/secure/user_passwords.txt

2025-11-12 16:15:22 - Password set for user 'light'

2025-11-12 16:15:23 - Processing user: siyoni (groups: sudo)

2025-11-12 16:15:23 - INFO: User 'siyoni' already exists. Ensuring groups and home perms.

2025-11-12 16:15:23 - Added user 'siyoni' to group 'sudo'

2025-11-12 16:15:23 - Set ownership and permissions on /home/siyoni

2025-11-12 16:15:24 - Processing user: manoj (groups: dev,www-data)

2025-11-12 16:15:24 - SUCCESS: Created user 'manoj' with supplementary groups: dev,www-data

2025-11-12 16:15:24 - Stored credentials for 'manoj' in /var/secure/user_passwords.txt

2025-11-12 16:15:24 - Password set for user 'manoj'

2025-11-12 16:15:24 - ===== User provisioning completed =====

2025-11-12 16:15:24 - Copied log output to sample_output/user_management.log.sample for review.

# Security Considerations

Security is a core focus of this user automation project.

The script manages sensitive account information, so several precautions are implemented to 

ensure safe and secure operation.

1.Password Protection

Generated passwords are stored only in:

passwords.txt

This file is created with:

chmod 600

meaning only the root user can read/write the file.

Passwords are never printed on the terminal.

2.Limited Exposure in Logs

The main log user_management.log does not contain passwords.

Only operational messages are logged (user actions, group creation, errors).

A documentation copy (sample_output/user_management.log.sample) is also password-free.

3.WSL Password Handling

WSL cannot update /etc/shadow for new users reliably.

To avoid security or system corruption issues, the script intentionally:

Detects WSL

Does not apply system passwords

Only stores them securely in passwords.txt

4.Secure Home Directory Permissions

Each user’s home directory is created or corrected with:

chmod 700


This ensures only the user has access to their home folder.

5.Safe Group & Username Handling

Whitespace and invalid formats are ignored.

Uppercase group names are automatically normalized to lowercase.

Script prevents injection or malformed username abuse by validating structure.

6.Least Privilege

Only sudo/root can run the script.

No unsafe operations are executed for non-root users.

 7.Cleanup Recommendation

After distributing account credentials, delete the password file:

sudo rm passwords.txt

or archive it securely in offline storage.

8.No External Dependencies

Script does not connect to the internet.

No external binaries are downloaded.

Everything runs locally inside WSL, reducing attack surface.

# Author

Name: Bunny Kukkunoori

Role: DevOps / SysOps Engineer

Project: User Management Automation (WSL-Based Linux User Provisioning)

GitHub: https://github.com/bunnykukkunoori-wq


