## Installing and Troubleshooting VirtualBox on Kali Linux

## 1. Project Overview

This project documents my experience installing Oracle VirtualBox on Kali Linux and troubleshooting the errors encountered during the installation process.

I initially attempted to install a VirtualBox package downloaded from the official website. However, the installation encountered dependency problems. I subsequently investigated the version available through Kali Linux's package repositories and worked through a kernel module issue before successfully launching VirtualBox.

This exercise helped me develop practical experience with Linux package management, kernel headers, DKMS, system services, and software verification.

## 2. Objective

The objective was to install and configure VirtualBox on my Kali Linux system so that I could create and run virtual machines for Linux administration and cybersecurity practice.

## 3. System Environment

Component| Details
Host operating system| Kali Linux
System architecture| x86-64
Virtualization software| Oracle VirtualBox
Package manager| APT
Kernel module management| DKMS

## 4. Initial Installation Attempt

I encountered the VirtualBox download link while taking a self-paced operating systems mini-course on the Cisco learning platform.

I followed the link to the official VirtualBox website:

https://www.virtualbox.org/

From the Linux distribution downloads section, I downloaded the package intended for Debian 13 on the AMD64 architecture.

When I attempted to install the downloaded package on Kali Linux, the installation reported that some required dependencies could not be provided by my configured package repositories.

This indicated that the downloaded package's dependencies could not be satisfied in my current environment.

Rather than repeatedly attempting the same installation, I decided to investigate the VirtualBox package available through Kali Linux's repositories.

## 5. Investigating the Available Package

Command
```
apt-cache policy virtualbox
```
Explanation

- "apt-cache" queries package information maintained by APT.
- "policy" displays package version and repository information.
- "virtualbox" specifies the package being investigated.

I used this command to examine the VirtualBox package available through my configured repositories.

This helped me identify the version offered by Kali Linux and choose an installation method that worked with the distribution's package management system.

## 6. Installing VirtualBox Through APT

Command
```
sudo apt install virtualbox
```
Explanation

- "sudo" runs the command with elevated privileges.
- "apt" invokes the Advanced Package Tool.
- "install" requests installation of a package.
- "virtualbox" identifies the package to install.

During installation, I encountered another error:

"No suitable module for running kernel found."

This indicated that VirtualBox's required kernel module was not available in a suitable form for the running kernel.

I investigated the kernel headers and DKMS to address the problem.

## 7. Troubleshooting the Kernel Module

## Step 1: Install the matching kernel headers

I used the following command:
```
sudo apt install linux-headers-$(uname -r)
```
The command requests the Linux kernel headers matching the currently running kernel.

The expression "$(uname -r)" is command substitution. The shell executes "uname -r" and inserts its output into the package name.

For example, if the running kernel is "7.1.5+kali-amd64", the command requests the corresponding kernel-header package.

These headers provide information needed to build kernel modules for the relevant kernel.

## Step 2: Rebuild DKMS-managed modules

After addressing the kernel-header requirement, I ran:
```
sudo dkms autoinstall
```
DKMS, or Dynamic Kernel Module Support, helps build and install registered kernel modules for supported kernel versions.

I used this command to trigger the installation or rebuilding of applicable DKMS-managed modules.

## Step 3: Verify DKMS status

I then checked the result:
```
dkms status
```
This command displays the status of modules registered with DKMS.

The output indicated that the VirtualBox module installation had succeeded.

The relevant module status was:

## virtualbox/7.2.16, 7.1.5+kali-amd64, x86_64: installed

This confirmed that the VirtualBox module was installed for the specified kernel and architecture.

## 8. Checking the VirtualBox Service

After working through the kernel module issue, I checked whether the VirtualBox service could be started.

## Command

```
sudo systemctl start virtualbox.service
```

This requests that systemd start the specified service.

I then checked its status:
```
systemctl status virtualbox.service
```
Observed result

## The output reported:

## Active: exited

This meant that systemd considered the service active, although its startup process had exited.

An "Active: exited" state can be normal for a service designed to perform its startup tasks and then finish. It is different from "Active: running", which indicates that a process remains running under the unit.

Therefore, I did not interpret this status alone as proof that all VirtualBox components were functioning correctly.

Instead, I considered it alongside the DKMS status and the successful launch of the VirtualBox graphical application.

## 9. Launching VirtualBox

After completing the troubleshooting steps, I launched VirtualBox by entering:
```
virtualbox
```
The application opened successfully.

This confirmed that I could launch the VirtualBox graphical interface and proceed with creating virtual machines.

The successful application launch, together with the DKMS status check, provided additional evidence that the installation had progressed successfully.

## 10. Lessons Learned

Through this exercise, I gained practical experience with:

- Investigating Linux package installation failures.
- Checking package versions and repository sources with APT.
- Installing software through a distribution's package manager.
- Understanding the relationship between Linux kernel headers and kernel modules.
- Using DKMS to manage kernel modules.
- Checking module installation status.
- Starting and inspecting system services with systemd.
- Distinguishing between an active service that has exited and one that is still running.
- Verifying software functionality after troubleshooting.

An important lesson was that an installation error should be investigated systematically rather than addressed by repeatedly attempting the same operation.

I also learned to distinguish between installing a software package, resolving its kernel module requirements, checking service status, and verifying that the application can actually launch.

## 11. Conclusion

I successfully installed and launched VirtualBox on Kali Linux after encountering package dependency and kernel module issues.

By investigating the package repositories, addressing the kernel module requirements, checking DKMS status, and launching the application, I gained practical experience in Linux software installation and troubleshooting.

This exercise prepared me to use VirtualBox for further Linux administration and cybersecurity laboratory exercises.

Official Reference

- Oracle VirtualBox: https://www.virtualbox.org/
