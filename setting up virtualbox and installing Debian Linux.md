Setting Up VirtualBox and Installing Debian Linux

## 1. Introduction

This lab documents the process of setting up Oracle VirtualBox on Kali Linux, creating a virtual machine, and installing Debian GNU/Linux.

The objective was to establish a separate Linux environment for practical system administration, troubleshooting, and cybersecurity exercises.

## 2. Lab Environment

Component| Configuration
Host operating system| Kali Linux
Virtualization software| Oracle VirtualBox 7.2.16
Guest operating system| Debian GNU/Linux 13 (Trixie)
Architecture| AMD64
Virtual memory| 2 GB
CPU allocation| 1 core
Virtual disk| Approximately 20 GB
Installation type| Command-line environment

## 3. Creating the Virtual Machine

1. Launch VirtualBox.
2. Click New in the top panel.
3. Enter a name for the virtual machine.
4. Select the downloaded Debian ISO image.
5. Check that VirtualBox identifies the appropriate operating system type and version.
6. Leave unattended installation unchecked to perform the installation manually.
7. Configure the virtual machine's memory, CPU and storage.

## Note: The ISO image and operating system can differ depending on the user's requirements. EFI configuration also depends on the guest operating system and boot requirements; in this lab, I used the default BIOS-based configuration.

## 4. Starting the Debian Installation

1. Select the Debian VM in VirtualBox Manager.
2. Click Start.
3. When the Debian boot menu appears, select Graphical install.
4. Follow the installation prompts to configure language, location, keyboard layout and other regional settings.

## Practical observation: When entering passwords, press each key deliberately. Holding down a key can cause repeated characters to be entered, potentially resulting in password confirmation errors.

## 5. Disk Partitioning

For this installation, I used guided partitioning.

1. Select Guided – use entire disk.
2. Select the virtual disk identified as the VirtualBox hard disk.
3. Choose All files in one partition.

## This is a straightforward arrangement for a beginner's lab because it keeps the filesystem structure relatively simple.

4. Review the proposed partition layout.
5. Select Finish partitioning and write changes to disk.
6. Confirm the changes when prompted.

## Important: This option applies to the VM's virtual disk. Make sure you have selected the correct virtual disk rather than a physical disk containing important data.

Allow Debian to install the base system.

## 6. Additional Installation Configuration

Continue through the installer:

- When asked to scan additional installation media, select No if you are using the downloaded installation ISO and do not have additional media.
- Select the appropriate country or package mirror location.
- When asked for an HTTP proxy, leave the field blank if you do not use one.
- Configure the network mirror when prompted.
- At Software Selection, choose the packages relevant to your intended environment.

For this lab, I wanted a lightweight, command-line Debian environment rather than a full desktop installation.

## 7. GRUB Bootloader Installation

During my installation, the installer first asked whether to install GRUB to the primary drive. I selected No.

The installer then presented another screen where I could select the device for GRUB installation. I selected the device identified as the VirtualBox hard disk.

## Important clarification: GRUB is the bootloader that allows the installed operating system to start. For a normal VirtualBox installation, it needs to be installed to the VM's virtual boot disk. The exact prompts and choices may vary with the installer and boot configuration.

## 8. Removing the Installation ISO

Before rebooting, remove the installation ISO from the VM's virtual optical drive.

In VirtualBox:

1. Open the Devices menu.
2. Select Optical Drives.
3. Eject or uncheck the attached Debian ISO.

This does not delete the downloaded ISO file. It simply disconnects it from the VM's virtual optical drive so that the installed system can boot from its virtual hard disk.

## 9. First Login

After the installation completes:

1. Allow the virtual machine to reboot.
2. Log in using the account credentials created during installation.
3. Confirm that Debian starts successfully.

## 10. Outcome

Debian GNU/Linux 13 was successfully installed inside VirtualBox as a lightweight command-line virtual machine.

The environment was subsequently used for Linux administration exercises, sudo configuration, SSH connectivity testing and other practical lab activities.

## 11. Conclusion

This exercise provided practical experience creating a virtual machine, installing a Linux distribution, configuring disk partitions, selecting software packages and installing a bootloader.

It also established an isolated Debian environment that could be used for further Linux administration and cybersecurity learning.
