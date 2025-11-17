*** 
# Arch Linux VM Installation

## 1. Obtain an Arch Linux iso
This can be found at the [ArchWiki](https://wiki.archlinux.org/title/Installation_guide) installation guide step 1.1. On the download page, scroll down and locate the heading "Worldwide" and follow one of the links under it to download an iso. 

## 2. Setup a VM with a Hypervisor
In this documentation, VMware Workstation will be used. For the setup, there is not preset option for it so select "Other Linux 5.x kernel 64-bit".  Go through the normal process of setting up a VM. For the memory the memory, allocate 2GB for the RAM. For the specific disk, allocate 20GB. 

## 3. Boot into Arch Linux VM and ensure it is in EFI mode
Start up the VM like you would any other VM. It should open up to a shell which you can type into. To check if the boot mode is EFI, input `cat /sys/firmware/efi/fw_platform_size` into the shell. If the VM is not in UEFI mode, this will appear `No such file or directory`. To set the VM to UEFI mode, access the .vmx configuration file and insert `firmware="efi` into the second line. Do not alter anything in this file. You should only be adding a line.

## 4. Determine if the network is configured 
Enter the command 'ip a'. A 'lo' connection should appear and another connection like 'ens33' should appear. If the 'ens33' does not appear with an inet address, the network has been configured. Enter 'ping ping.archlinx.org' and if something like '64  bytes...' appears, your network is configured

## 5. Partition the disks

### 5.1. Enter `fdisk /dev/sda` 
This should direct you to a partitioning portal which will allow you to alter partitions 

### 5.2. Add the EFI partition 
Enter `n`. This adds a new partition. For the type, select `primary`. For the `First Sector`, use the default, so just press enter. For the `Second Sector`, enter `+500M` or the size you would like for this partition. This should finalize the partition. To change the type of this parition from `Linux` to `EFI`, enter `t` then enter `EF`.

### 5.3. Add the Arch OS partition 
Follow the same steps for adding the UEFI partition; however, when prompted to specify the size for the second sector, click enter to size the partition to the rest of the disk. The type of partition is defaulted to `Linux`, so the type of this one does not need to be changed. 

## 6. Format the partitions
Enter `mkfs.ext4 /dev/{Arch OS partition name}` for the Arch OS partition. 

Enter `mkfs.fat -F 32 /dev/{EFI partition name}` UEFI partition.

To find the names, enter `lsblk` and look at the sub-partitions of the disk. The partition with `500M` or the ammount you specified is the EFI and the other is the Arch OS. Generally, the EFI partition is smaller.

## 7. Mount the file systems
Enter `mount /dev/{Arch OS partition name} /mnt` for the Arch OS partition.

Enter `mount --mkdir /dev/{EFI partition name} /mnt/boot` for the EFI partition.

## 8. Configure the system 
1. Enter `genfstab -U /mnt >> /mnt/etc/fstab` to get needed file systems on startup

2. Enter the created environment with `arch-chroot /mnt` to further configure the system

3. Set the time with `ln -sf /usr/share/zoneinfo/{Your region}/{Your city} /etc/localtime`

4. Run `hwclock --systohc` to update the time configuration

5. Run `locale-gen` to generate locales 

6. Run `nano /etc/locale.conf` and add `LANG=en_US.UTF-8` to the file 

7. Set a name for your machine with `nano /etc/hostname` and add your hostname of choice to the file

8. Test network configuration with `ping www.google.com`. If `64 bytes...` appears, you are connected to the internet.

9. Set the root password with `passwd`


## 9. Install the Boot Loader GRUB
While still in the arch-chroot /mnt environment, run `pacman -S grub efibootmgr` to install the necessary packages

To configure GRUB to the EFI system partition, run `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`.

To generate the main GRUB configuration file, run `grub-mkconfig -o /boot/grub/grub.cfg`.

## 10. Reboot system and enjoy the Arch Linux VM
Run `umount -R /mnt` to unmount any mounts which could cause error on reboot. 

Then, run `reboot` to reboot the system. If setup and installation was successfull, you should be prompted to login. When prompted to login, enter `root` for login and the password that you set in step 8.9. HAVE FUN!!

# Customizations

## 1. Create user accounts 
Create a user account for yourself with `useradd -m -G wheel -s /bin/bash {username}`. The `-m` creates a home directory, `-G wheel` adds the user to the admin group which is called wheel for Arch, and `-s /bin/bash` assigns a login shell for the user. Run `passwd {username}` to set a password for the user. 

Run the same commands for a user named `codi`

## 2. Install `sudo`
The `sudo` command is not pre-installed in Arch, so you have to manually install it with `pacman -S sudo`

## 3. Give `sudo` privileges to users
Run `EDITOR=nano visudo`. This should open a file which you should change the line `# %wheel ALL=(ALL:ALL) ALL` to `%wheel ALL=(ALL:ALL) ALL`. 

## 4. Install zsh or any other shell 
Run `pacman -S zsh` to install and run `chsh -s /bin/zsh` to assign to a user

## 5. Install and enable `ssh`
Run `pacman -S openssh` to install and run `systemctl enable --now sshd` to enable.

## 6. Add color to `pacman` install manager
Run `nano /etc/pacman.conf` and change the line `#Color` to `Color`.

## 7. Add color to `bash` terminal 
Run `nano .bashrc` in the home folder of the user with the bash shell to access the bash shell config file. Edit the `PS1=` line to `PS1='\[\033[1;32m\]\u@\h \[\033[1;34m\]\W\[\033[0m\]\$ '`

## 8. Add color to `zsh` terminal 
Run `nano .zshrc` in the home folder of the user with the zsh shell to access the zsh shell config file. Add the line `PROMPT='%F{green}%n@%m %F{blue}%~%f %# '`

## 9. Install the DE LXQT or any other DE
Run `pacman -S lxqt openbox sddm` to install the DE and the necessary windows manager and display manager. To start the DE on boot, run `systemctl enable --now sddm` which will automatically reboot your system into the DE.

For anyother DE, ensure that you install a display manager which will allow the DE to be opened on boot. 

## 10. Add aliases
To add aliases, access the shell configuration file like .zshrc for zsh and .bashrc for bash with `nano`. In this file, add aliases by adding a line like `alias {replacemnt}='{command being replaced}'`. For example, changing `clear` command to `c`: `alias c='clear`.

# Issues

## - Network Down
The network went down after shutting down the base device. The mirrors for install could not be accessed. Run `systemctl enable --now NetworkManager` to re-link network.

## - Create user accounts before booting with DE 
If you boot into the VM through LXQT without creating any user accounts, the login prompt will be blank. To return to the bash terminal again, press Ctrl+Alt+f3. 


