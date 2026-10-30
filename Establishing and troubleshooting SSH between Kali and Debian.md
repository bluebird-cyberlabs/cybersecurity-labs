## Establishing and Troubleshooting an SSH Connection Between Kali Linux and a Debian Virtual Machine

# 1. Introduction

This lab documents how I established a Secure Shell (SSH) connection from my Kali Linux host to a Debian virtual machine running inside Oracle VirtualBox.

The objective was to establish remote terminal access, authenticate using the Debian user account and execute commands on the remote operating system.

# 2. Lab Environment

Component| Configuration
Host operating system| Kali Linux
Guest operating system| Debian GNU/Linux 13
Virtualization software| Oracle VirtualBox
Network mode| NAT
SSH protocol| SSH over TCP
Debian SSH port| 22
Kali forwarded port| 2222
Debian account| bluebird

# Understanding the SSH roles

- Kali Linux: SSH client. It initiates the connection.
- Debian: SSH server. It accepts the connection and provides remote terminal access.

The Debian SSH server must be running for this connection to succeed. Kali's SSH server does not need to run because Kali is acting as the client.

# 3. Step 1: Start and Log In to the Debian VM

1. Open VirtualBox Manager.
2. Select the Debian virtual machine.
3. Click Start.
4. Log in using the Debian account credentials.

# 4. Step 2: Check the SSH Service on Debian

I checked whether the SSH server was running:
```
systemctl status ssh
``` 
# Explanation:

- "systemctl" manages and inspects systemd services.
- "status" displays the current service state.
- "ssh" identifies the SSH service.

# Observed result: "active (running)".

This confirmed that the SSH server was running on Debian.

# 5. Step 3: Configure VirtualBox NAT Port Forwarding

My Debian VM was configured to use NAT networking.

With the default VirtualBox NAT configuration, the guest can generally initiate outbound connections, but the host cannot directly initiate a connection to the guest using its private NAT address.

To make SSH access possible, I configured port forwarding.

# 5.1 Shut Down the Debian VM

Before modifying the network settings, I shut down the virtual machine:
```
sudo poweroff
```
# Explanation:

- "sudo" executes the command with administrative privileges.
- "poweroff" requests a system shutdown.

# 5.2 Open the Port Forwarding Settings

In VirtualBox Manager:

1. Select the Debian VM.
2. Open Settings.
3. Navigate to Network.
4. Select Adapter 1.
5. Expand Advanced.
6. Open Port Forwarding.
7. Click the add-rule button.

# 5.3 Configure the Port Forwarding Rule

I configured the following values:

Field| Value| Explanation
Name| Debian-SSH| Identifies the forwarding rule
Protocol| TCP| SSH uses TCP
Host IP| 127.0.0.1| Restricts access to the local host
Host Port| 2222| Port used by the Kali host
Guest IP| Blank| Allows VirtualBox to use the guest's NAT address
Guest Port| 22| Default SSH server port on Debian

# How Port Forwarding Works

The connection follows this path:

Kali Linux
127.0.0.1:2222
       |
       v
VirtualBox NAT Port Forwarding
       |
       v
Debian VM
Guest TCP Port 22
       |
       v
Debian SSH Server

Using "127.0.0.1" as the host IP restricts the forwarded host-side connection to Kali itself rather than exposing that forwarding rule on all host network interfaces.

# 6. Step 4: Start Debian and Connect from Kali

After saving the forwarding rule:

1. Start the Debian VM.
2. Log in to Debian.
3. Open a terminal on Kali.

I then initiated the SSH connection using:
```
ssh -p 2222 bluebird@127.0.0.1
```
# Command Explanation

- "ssh" launches the SSH client.
- "-p 2222" specifies the host-side port to connect to.
- "bluebird" identifies the user account on Debian.
- "127.0.0.1" is Kali's loopback address.

VirtualBox forwards this connection from Kali's port 2222 to Debian's port 22.

# First-Time Connection Authentication

On the first connection, SSH may display a host authenticity warning and ask whether the server's host key should be trusted.

After verifying that the connection is to the intended lab machine, I accepted the host key by typing:

# yes

SSH then requested the password for the Debian "bluebird" account.

# Practical observation:
Initially, I entered my Kali password because both systems used the same username. Authentication failed. I then entered the Debian account password, which worked.

This demonstrated that SSH authenticates against the remote machine's user account, not the local machine's password.

The connection succeeded, and the Debian command prompt became accessible from my Kali terminal.

# 7. Step 5: Verify the Remote System

After connecting, I checked the hostname:
```
hostname
```
This displays the hostname of the system whose shell is currently active.

Both machines used the hostname "bluebird", so this check alone could not conclusively distinguish them.

I therefore checked the operating system:
```
cat /etc/os-release
```
# Explanation:

- "cat" reads and displays file contents.
- "/etc/os-release" contains operating system identification information.

# Observed result:

PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
VERSION="13 (trixie)"
VERSION_CODENAME=trixie
DEBIAN_VERSION_FULL=13.7
ID=debian

This confirmed that the active SSH shell was running on Debian GNU/Linux 13 (Trixie).

# 8. Step 6: Create a File Through the SSH Session

To verify that commands entered through SSH were actually executing on Debian, I created a test file while still connected.

# Command:
```
echo "From the SSH connection on Kali" > ssh_test.txt
```
# Explanation:

- "echo" outputs the supplied text.
- The quoted text is the content to write.
- ">" redirects the output into a file.
- "ssh_test.txt" is the filename.

The file was created in the current working directory on Debian because the command was executed in the remote Debian shell.

Important: Although I entered the command through Kali, the file was stored on Debian's virtual disk.

# 9. Step 7: Verify the File Inside the Debian VM

I exited the SSH session:
```
exit
```
This closed the remote shell and returned me to the local Kali terminal. It did not shut down Debian.

I then opened the Debian VM's own terminal and checked the directory:
```
ls
```
The output included:

# ssh_test.txt

To verify the file contents, I ran:
```
cat ssh_test.txt
```
# Observed output:

From the SSH connection on Kali

This confirmed that the file created through the SSH session was stored on Debian and could be accessed directly from the Debian VM.

# 10. Results

Test| Result
Debian SSH service| Active and running
VirtualBox NAT port forwarding| Configured
SSH connection from Kali| Successful
Debian account authentication| Successful
Remote OS identification| Debian GNU/Linux 13
Remote file creation| Successful
File verification inside Debian VM| Successful

# 11. Lessons Learned

This exercise provided practical understanding of:

1. The difference between an SSH client and an SSH server.
2. The role of TCP port 22 in SSH communication.
3. How VirtualBox NAT port forwarding enables host-to-guest connectivity.
4. Why the remote system's account password is required.
5. How to verify the identity of a remote operating system.
6. How commands executed through SSH affect the remote machine's filesystem.

# 12. Conclusion

I successfully established an SSH connection from Kali Linux to my Debian virtual machine using VirtualBox NAT port forwarding.

I verified the remote operating system and demonstrated remote command execution by creating a file through the SSH session and reading it directly inside the Debian VM.

This exercise strengthened my understanding of remote Linux administration and provides a foundation for further work with SCP and SFTP for secure file transfers.

Next planned lab: Secure File Transfer Between Kali Linux and Debian Using SCP.
