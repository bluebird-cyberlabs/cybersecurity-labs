## Troubleshooting Sudo Installation and User Permissions on Debian Linux

## 1. Introduction

This lab documents how I investigated and resolved issues preventing a standard user account from executing administrative commands with "sudo" on Debian Linux.

The troubleshooting process involved three separate areas:

1. Command availability.
2. User authorization.
3. Login session group membership.

## 2. Problem Identification

During the exercise, I encountered the following problems:

Problem| Cause| Resolution
"sudo: command not found"| The sudo package was not installed.| Installed sudo as root.
User is not in the sudoers file| The user lacked sudo authorization.| Added the user to the sudo group.
Sudo group did not appear in "id"| The existing login session had not refreshed its group membership.| Logged out and logged back in.

## 3. Troubleshooting Step 1: Check Whether Sudo Is Installed

The first question was whether the command existed on the system.

Command:
```
command -v sudo
```
## Explanation:

- "command" is a shell built-in used to identify or execute commands.
- "-v" asks the shell to display how a command would be resolved.
- "sudo" is the command being checked.

If sudo is installed and available through the user's PATH, the command displays its location.

In my case, sudo was unavailable.

## 4. Troubleshooting Step 2: Install Sudo

Since sudo was not installed, I switched to the root account.
```
su -
```
## Explanation:

- "su" switches to another user account.
- "-" requests a login shell, loading the target user's login environment.
- With no username specified, the command switches to root.

After obtaining root access, I installed sudo:
```
apt install sudo
```
## Explanation:

- "apt" is Debian's package management command-line tool.
- "install" tells APT to install a package.
- "sudo" is the package name.

The package was installed successfully.

## 5. Troubleshooting Step 3: Grant Sudo Access

Installing sudo does not automatically authorize every user to use it.

I added my user account to the sudo group:
```
usermod -aG sudo bluebird
```
## Explanation:

- "usermod" modifies an existing user account.
- "-a" means append, preserving existing supplementary group memberships.
- "-G" specifies supplementary groups.
- "sudo" is the group to add.
- "bluebird" is the target username.

# Important: Using "-aG" is important. Using "-G" without "-a" can replace the user's existing supplementary group list.

## 6. Troubleshooting Step 4: Refresh Group Membership

After adding the user to the sudo group, the expected group membership did not immediately appear in the existing session.

I logged out completely and logged back in.

This was necessary because an existing login session generally retains the group memberships it received when the session started.

After logging back in, I checked the group memberships:
``
id
``
The output showed that the "sudo" group was now included.

## 7. Final Verification

I tested sudo by running:
```
sudo apt update
```
The command executed successfully, confirming that:

- The sudo package was installed.
- The user had the required authorization.
- The updated group membership was active in the new session.

## 8. Important Lessons

This exercise taught me to investigate command and permission problems in separate stages.

# Availability: Does the command exist?
```
command -v sudo
```
# Authorization: Is the user permitted to execute administrative commands?
```
usermod -aG sudo bluebird
```
# Session state: Has the current login session loaded the updated group membership?
```
id
```
## 9. Conclusion

The issue was resolved by installing the missing sudo package, adding the user account to the sudo group and starting a new login session.

The most important lesson was that command availability, user authorization and session state are separate things. Troubleshooting them individually makes it easier to identify the actual cause of a problem.
