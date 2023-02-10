# An intuitive way to install ArchLinux 

> by Felipe Facundes

##### Telegram Channel: [https://t.me/s/PlayOnGit](https://t.me/s/PlayOnGit)
##### Site: [https://linuxgamers.github.io/](https://linuxgamers.github.io/)

<br>

---
## Step 01 - Connect to the Internet. 
---
<br>

#### Check your Wifi network interface:
`iwconfig`

#### Enter the IWD interface:
`iwctl`

#### Inside the interface, type the following commands: 

 - `device list`

#### To start searching the network assuming your device is `wlan0`

 - `station wlan0 scan`

#### To list available networks (SSID) 

 - `station wlan0 get-networks`

#### Finally, connecting to the Internet 

 - `station wlan0 connect SSID`

<br>

---
## Optional Step - Backup - For a system reinstall without formatting
---
<br>

#### BACKUP CAN BE, FOR A LIST. 
<br>

 - For a reinstall, downloading the packages again: 

##### Read. In the line below contains `2` command lines, obey each command: 

 1. - `pacman -Qnq | tee list.txt &>/dev/null`
 2. - `pacman -S $(cat list.txt)`

<br>

#### OR YOU CAN REUSE EXISTING PACKAGES FROM YOUR HD WITH THIS METHOD: 
<br>

##### Read. In the line below contains `3` command lines, obey each command: 
#### First mount the root partition `(/)`
 
 1. - `mount /dev/sdX /mnt`
 2. - `cd /mnt`
 3. - `rm -rf bin dev lib lib64 mnt opt proc root run sbin srv sys tmp usr`

#### Analyze if there is subvolume interfering:
##### Read. In the line below contains `2` command lines, obey each command: 

 1. - `btrfs subvol list -a /mnt/`
 2. - `btrfs subvol delete /mnt/var/lib/machines`

#### Back up existing packages in the cache: 
##### Read. In the line below contains `13` command lines, obey each command: 

 1. - `cd /mnt/var/cache/pacman/pkg/`
 2. - `mkdir pkg_bkp`
 3. - `mv *.pkg.* pkg_bkp/`
 4. - `mv pkg_bkp /mnt/`
 5. - `cd /mnt`
 6. - `rm -rf var`
 7. - `mkdir -p /mnt/var/cache/pacman/`
 8. - `mkdir -p /mnt/var/lib/machines/`
 9. - `mkdir -p /mnt/proc/bus/usb/`
 10. - `mv pkg_bkp pkg`
 11. - `mv pkg /mnt/var/cache/pacman/`
 12. - `cd /mnt/boot/`
 13. - `rm -rf *`

<br>

---
## Step 02 - Partitioning and Formatting: 
---
<br>

### Partition the HD
<br>

#### Create `"sda1"` 250MB to boot - If it is `UEFI` the partition of `BOOT` have to be in `FAT`. 
#### Create `"sda2"` a partition for the root `(/)` of the system `(root)` of at least 30GB. 
#### Create `"sda3"` 512MB or 3GB for swap/ `3GB if you want sleep mode` - can be a larger size, up to the same number of your RAM

<br>

#### To partition use these commands:
#### To check existing partitions:

 1. - `blkid`
 2. - `fdisk -l`

#### To quickly zero the HD and create a new partition table:
`cfdisk -z /dev/sda`

#### To just create partitions within an existing partition table:
`cfdisk /dev/sda`

#### For another partitioner in text mode, very efficient by the way, in my opinion the best: `parted`
`parted /dev/sda`

#### To properly format each linux partition. Format in ext4 64Bits or XFS.
#### Example:
 1. - `mke2fs -text4 -O 64bit /dev/sdX`

###### Or
 2. - `mkfs.xfs /dev/sdX`

<br>

#### `EXT4` and more `compatible` with DESKTOP programs: games, etc. Not to mention that ext4 is a mature system. Which supports improper shutdown. 
#### On the other hand `XFS` is the fastest file system with proper support for `SSD`s. Equally, it is a mature system with Journaling. 

 1. - `mke2fs -text4 -O 64bit -L ROOT /dev/sdX`

###### Ou

 2. - `mkfs.xfs -L ROOT /dev/sdX`

<br>

### For `UEFI`:

<br>

#### The partition `/boot` or `/boot/EFI` already have to be in `FAT-12/16/32`
#### It is worth noting that a partition `FAT12` have to have at least `1M`. already the `FAT16` with at least `9M` and `FAT32` with at least `33M`
#### If you are going to use it as `/boot` have to have at least `250M` as `/boot/EFI` is the minimum indicated above corresponding to the chosen `FAT` partition type. 

<br>

#### Use `-F12` for FAT12. Example:
 - Use: `-s2` to reduce cluster size and make the partition readable by UEFI. 
 - `mkfs.fat -F12 -s2 -n EFI /dev/sdX`
 - Else: 
   - `mkfs.fat -F12 -n EFI /dev/sdX`

<br>

#### FORMATTING EXAMPLE 

#### The option `-L` assigns labels to partitions, which helps to query them later through `/dev/disk/by-label` without having to remember your numbers. Now mount your partitions:

```
mkfs.fat -F12 -n EFI /dev/sd1               # <‐ EFI partition, Required for EFI systems.
mke2fs -text4 -O 64bit -L BOOT /dev/sda2    # <‐ BOOT partition, is Optional, but good practice to use separate BOOT partition in case of errors.
mkfs.xfs -L ROOT /dev/sda3                  # <‐ ROOT partition.
mkfs.xfs -L HOME /dev/sda4                  # <‐ HOME partition, Optional.
mkswap -L SWAP /dev/sda5                    # <‐ SWAP partition, Optional.  
```

#### PARTITION TABLE ASSEMBLY EXAMPLE, ACCORDING TO FORMATTING ABOVE: 
##### Read. In the line below contains `7` command lines, obey each command: 

<pre>
 1. - <code>swapon /dev/sda5</code>                # <- Enable SWAP
 2. - <code>mount /dev/sda3 /mnt</code>            # <- Mounting the ROOT Partition
 3. - <code>mkdir -p /mnt/home</code>              # <- Create home folder
 4. - <code>mount /dev/sda4 /mnt/home</code>        # <- Mount HOME Partition 
 5. - <code>mkdir -p /mnt/boot/EFI</code>           # <- Create boot folder and EFI 
 6. - <code>mount /dev/sda2 /mnt/boot</code>        # <- Mount the BOOT Partition 
 7. - <code>mount /dev/sda1 /mnt/boot/EFI</code>     # <- Mount the EFI Partition 
</pre>
<br>

---
## Step 03 - FINALLY, LET'S GO TO INSTALLATION:
---
<br>

### To just load the keyboard layout for US:
##### Read. In the line below contains `2` command lines, obey each command: 

 1. - `loadkeys us`
 2. - `export LANG=en_US.UTF-8`
 
##### Read. In the line below contains `2` command lines, obey each command: 

 1. - `pacstrap -i /mnt grub base wget base-devel linux mkinitcpio nano`
 2. - `genfstab -U -p /mnt >> /mnt/etc/fstab`

<br>

### Now it's inside the system (chroot):
`arch-chroot /mnt`

### For encrypted HDs, that is, only, if you deliberately encrypted your HD, to do so, follow this LINK from my tutorial: 

[https://github.com/felipefacundes/desktop/tree/master/GRUB](https://github.com/felipefacundes/desktop/tree/master/GRUB)

<br>

### In order for the system to start correctly, install GRUB:
##### Read. In the line below contains `2` command lines, obey each command: 

 1. - `pacman -S grub ntfs-3g fuse2 dosfstools efibootmgr exfat-utils mtools gpart lzop udftools fuseiso libisoburn sdl xz gettext device-mapper bash-completion bash freetype2 xfsprogs polkit`

 2. - `mkinitcpio -P`

<br>

### For systems `UEFI`

<br>

#### The partition `/boot` or `/boot/EFI` already have to be in `FAT-12/16/32`. As already mentioned.  
`mkfs.fat -F12 -n EFI /dev/sdX`

<br>

#### Now prepare GRUB for UEFI:
 - #### If the EFI partition is separated and mounted in the folder `/boot/EFI`:
`grub-install --verbose --recheck --target=x86_64-efi --force --efi-directory=/boot/EFI --bootloader-id=ARCH --removable`

 - #### Or if the entire BOOT partition is on FAT and mounted on `/boot`:
`grub-install --verbose --recheck --target=x86_64-efi --force --efi-directory=/boot --bootloader-id=ARCH --removable`

<br>

### For BIOS (i386-pc):
 - Use `--force` if partition table is GPT
 - `grub-install --verbose --recheck --target=i386-pc --force /dev/sda`

<br>

### Finish with:
`grub-mkconfig -o /boot/grub/grub.cfg`

<br>

### If you dual boot with Rwindows, install the following "os-prober", then repeat the above command, or do it before running: 
`pacman -S os-prober`

<br>

### "Root password"
`passwd root`

<br>

### Create your username: Don't forget to change the name: YourPreferenceUser <-- To your preferred Username. No accents. Example: john
##### Read. In the line below contains `2` command lines, obey each command: 

1. - `useradd -m -g users -G daemon,disk,wheel,rfkill,dbus,network,video,audio,storage,power,users,input -s /bin/bash YourPreferenceUser`

2. - `usermod -a -G daemon,disk,wheel,rfkill,dbus,network,video,audio,storage,power,users,input YourPreferenceUser`

<br>

### Creating a password, for your user: 
`passwd YourPreferenceUser`

<br>

### Editing SUDOers to have admin access:
`nano /etc/sudoers`

<br>

#### Look for the line: `"root ALL=(ALL) ALL"`
#### And just below include your username like this: `YourPreferenceUser ALL=(ALL) ALL`
```
root ALL=(ALL) ALL
YourPreferenceUser ALL=(ALL) ALL
```

<br>

### For XORG - That is, without it you will not have a graphical interface, it is extremely important:
`pacman -S xorg-xinit xorg-server xorg-server-devel`

<br>

### If you dual boot with Windows, use: 
##### Read. In the line below contains `3` command lines, obey each command: 

1. - `hwclock --systohc --localtime`
2. - `echo -e "HARDWARECLOCK="localtime" >> /etc/locale.conf"`
3. - `echo -e "echo -e "UTC=no" >> /etc/locale.conf"`

<br>

### To your hostname:
`echo ArchLinux > /etc/hostname`

<br>

### To have internet: 
##### Read. In the line below contains `3` command lines, obey each command: 

1. - `pacman -S wireless_tools wpa_supplicant network-manager-applet networkmanager`
2. - `systemctl enable NetworkManager.service`
3. - `systemctl start NetworkManager.service`

<br>

---
## Step 04 - Drivers 
---
<br>

### For Nvidia drivers: 

#### Enable Multilib in `/etc/pacman.conf`
#### Strip the hashtag before the two lines: `[multilib]` and Include = `/etc/pacman.d/mirrorlist`
```
pacman -Syy nvidia-dkms linux-headers dkms nvidia-settings lib32-libvdpau lib32-libglvnd libglvnd libvdpau nvidia-utils opencl-nvidia xsettingsd xsettings-client ffnvcodec-headers libxnvctrl xf86-video-nouveau lib32-nvidia-utils lib32-opencl-nvidia nccl nvidia-cg-toolkit
```

<br>

### For intel driver: 
#### Enable Multilib in `/etc/pacman.conf`
#### Strip the hashtag before the two lines: `[multilib]` and Include = `/etc/pacman.d/mirrorlist`
```
pacman -Syy lib32-vulkan-intel lib32-mesa lib32-libva1-intel-driver lib32-libva-intel-driver libva1-intel-driver libva-utils intel-opencl-clang intel-media-driver intel-graphics-compiler lib32-libglvnd libglvnd linux-headers dkms intel-gpu-tools intel-gmmlib intel-compute-runtime i810-dri xf86-video-intel vulkan-intel mesa libva-intel-driver iucode-tool intel-ucode intel-tbb
```

<br>

### For AMD driver: 

#### Enable Multilib in `/etc/pacman.conf`
#### Strip the hashtag before the two lines: `[multilib]` and Include = `/etc/pacman.d/mirrorlist`
```
pacman -Syy opencl-mesa xf86-video-amdgpu xf86-video-ati linux-headers dkms vulkan-devel lib32-libglvnd libglvnd vulkan-radeon lib32-vulkan-icd-loader vulkan-icd-loader lib32-vulkan-validation-layers vulkan-validation-layers
```

<br>

### Para o driver de Áudio:

#### Enable Multilib in `/etc/pacman.conf`
#### Strip the hashtag before the two lines: `[multilib]` and Include = `/etc/pacman.d/mirrorlist`

    pacman -Syy alsa-plugins alsa-utils lib32-alsa-plugins lib32-alsa-lib lib32-libpulse lib32-libcanberra-pulse pulseaudio-equalizer-ladspa ponymix pulseaudio-qt pulseaudio-lirc pulseaudio-jack pulseaudio-equalizer pulseaudio-bluetooth pulseaudio-alsa pulseaudio pavucontrol libpulse libcanberra-pulse libao lib32-libpulse qjackctl jack2 lib32-jack2 libffado --noconfirm

<br>

### You can enable Radv, for your AMDGPU RADEON, so follow my tutorial, super easy: 

https://amdgpu.github.io/

<br>

---
## Step 05 - Desktop 
---
<br>

### Workspaces, choose either one or the other. Among them are: KDE, Cinnamon, GNOME, DEEPIN, XFCE, MATE  

<br>

### For Plasma kde:
##### Read. In the line below contains `2` command lines, obey each command: 

1. - `pacman -S kf5-aids kate nomacs gimp krita packagekit packagekit-qt5 discover okular kf5 plasma plasma-wayland-session plasma-mediacenter qtav mpv youtube-dl vlc sddm firefox-i18n-pt-br firefox plasma-pa xdg-user-dirs`

2. - `systemctl enable sddm`


<br>

### To install Cinnamon: 
##### Read. In the line below contains `2` command lines, obey each command: 
1. - `pacman -S cinnamon lightdm-gtk-greeter lightdm gimp viewnior firefox firefox-i18n-pt-br xdg-user-dirs`
2. - `systemctl enable lightdm`

<br>

### To install GNOME: 
##### Read. In the line below contains `2` command lines, obey each command: 

1. - `pacman -S gnome gnome-extra gnome-shell gdm gimp viewnior firefox firefox-i18n-pt-br xdg-user-dirs`
2. - `systemctl enable gdm`

<br>

### To install DEEPIN: 
##### Read. In the line below contains `3` command lines, and a command alternative. Obey each command, and read the alternative: 
1. - `pacman -S deepin-control-center deepin-daemon deepin-api deepin-desktop-base deepin-desktop-schemas deepin-dock deepin-gtk-theme deepin-launcher deepin-menu deepin-network-utils deepin-polkit-agent-ext-gnomekeyring deepin-qt5dxcb-plugin deepin-qt5integration deepin-session-ui deepin-shortcut-viewer deepin-sound-theme deepin-system-monitor deepin-wallpapers startdde lightdm-gtk-greeter lightdm gimp viewnior firefox firefox-i18n-pt-br xdg-user-dirs`

2. - `systemctl enable lightdm`

3. - `pacman -Rdd deepin-anything deepin-anything-dkms`
#### Or, if you don't have deepin-anything-dkms: 
3. - `pacman -Rdd deepin-anything`

<br>

### To install XFCE: 
##### Read. In the line below contains `2` command lines, obey each command: 

1. - `sudo pacman -S xfce4 xfce4-goodies lightdm-gtk-greeter lightdm gimp viewnior firefox firefox-i18n-pt-br xdg-user-dirs`
2. - `systemctl enable lightdm`

<br>

### To install MATE: 
##### Read. In the line below contains `2` command lines, obey each command: 
1. - `sudo pacman -S mate mate-extra lightdm-gtk-greeter lightdm gimp viewnior firefox firefox-i18n-pt-br xdg-user-dirs`
2. - `systemctl enable lightdm`

<br>

### KDE uses 800MB RAM, Cinnamon and GNOME use 750MB RAM, DEEPIN uses 700MB RAM, XFCE and MATE use 650MB RAM 
##### To install XMATECE, an interface as beautiful and complete as MATE, but which uses less than 300MB of RAM, follow this tutorial: 

[https://github.com/felipefacundes/xmatece](https://github.com/felipefacundes/xmatece)

<br>

---
## Step 06 - For those who want to use the `startx` rather than `SDDM/GDM/LightDM` and etc 
---
<br>

### For TTY Autologin - GETTY - here is for autologin, WITHOUT NEEDING DM (Desktop Manager), like: lightdm, GDM, SDDM and etc: 
##### Read. The line below contains `3` command lines, obey each command: 
1. - `mkdir -p /etc/systemd/system/getty@tty1.service.d/`
2. - `echo -e "[Service]" > /etc/systemd/system/getty@tty1.service.d/override.conf`
3. - `echo -e "ExecStart=" >> /etc/systemd/system/getty@tty1.service.d/override.conf`

<br>

### Don't forget to change YourPreferenceUsername <-- To your preferred username. No accents. Example: john
##### Read. In the line below contains `4` command lines, obey each command: 
1. - `echo -e "ExecStart=-/usr/bin/agetty --autologin YourPreferenceUser --noclear %I $TERM" >> /etc/systemd/system/getty@tty1.service.d/override.conf`
2. - `mkdir -p /etc/systemd/system/serial-getty@ttyS0.service.d/`
3. - `echo -e "[Service]" > /etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf`
4. - `echo -e "ExecStart=" >> /etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf`

<br>

### Don't forget to change YourPreferenceUsername <-- To your preferred username. No accents. Example: john
`echo -e "ExecStart=-/usr/bin/agetty --autologin YourPreferenceUser -s %I 115200,38400,9600 vt102" >> /etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf`

<br>

### Start SERVICE
`systemctl enable getty@.service`

<br>

---
## Step 07 - Adjustments for: PERFORMANCE / GAMES / SECURITY 
---
<br>

### Prepare for games. All the necessary dependencies, including to considerably increase the performance in games: 
#### Enable Multilib in `/etc/pacman.conf`
#### Strip the hashtag before the two lines: `[multilib]` and Include = `/etc/pacman.d/mirrorlist`
```
pacman -Syy --noconfirm egl-wayland eglexternalplatform libglvnd glfw-x11 clinfo opencl-headers opencl-mesa intel-opencl-clang libclc ocl-icd lib32-ocl-icd lib32-libglvnd lib32-glu glu libva-mesa-driver mesa mesa-demos mesa-vdpau lib32-mesa lib32-mesa-demos lib32-mesa-vdpau lib32-smpeg lib32-sdl_ttf lib32-sdl_mixer lib32-sdl_image lib32-sdl2_ttf lib32-sdl2_mixer lib32-sdl2_image lib32-sdl2 lib32-sdl sdl sdl2 sdl2_image sdl2_mixer sdl2_ttf sdl_image sdl_mixer sdl_ttf smpeg lib32-openal gambas3-gb-openal alure openal-examples openal freealut ffnvcodec-headers xf86-video-nouveau nvidia-cg-toolkit steam-native-runtime lib32-gtk3 vulkan-devel attr lib32-attr fontconfig lib32-fontconfig lcms2 lib32-lcms2 libxml2 lib32-libxml2 libxcursor lib32-libxcursor libxrandr lib32-libxrandr libxdamage lib32-libxdamage libxi lib32-libxi gettext lib32-gettext freetype2 lib32-freetype2 linux-headers dkms libsm lib32-libsm gcc-libs lib32-gcc-libs libpcap lib32-libpcap desktop-file-utils giflib lib32-giflib libpng lib32-libpng gnutls lib32-gnutls libxinerama lib32-libxinerama libxcomposite lib32-libxcomposite libxmu lib32-libxmu libxxf86vm lib32-libxxf86vm libldap lib32-libldap mpg123 lib32-mpg123 openal alsa-lib lib32-alsa-lib libxcomposite lib32-libxcomposite mesa-libgl lib32-mesa-libgl opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libpulse lib32-libpulse libva lib32-libva gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader sdl2 lib32-sdl2 vkd3d lib32-vkd3d gsm ffmpeg xf86-video-ati xf86-video-amdgpu xf86-video-intel xf86-video-nouveau libva-intel-driver libva-utils libva-vdpau-driver vulkan-intel libgphoto2 ncurses lib32-ncurses libjpeg-turbo lib32-libjpeg-turbo lib32-alsa-plugins vulkan-radeon lib32-vulkan-intel lib32-vulkan-radeon lib32-vulkan-validation-layers wine-staging
```

<br>

### For codecs (codecs, are extremely important in the system, for better multimedia harmony: sound and video): 
#### Enable Multilib in `/etc/pacman.conf`
#### Strip the hashtag before the two lines: `[multilib]` and Include = `/etc/pacman.d/mirrorlist`
```
pacman -S lib32-libcanberra-gstreamer lib32-gstreamer lib32-gst-plugins-good lib32-gst-plugins-base-libs lib32-gst-plugins-base aribb24 gpac gst-libav lame libdvbpsi libiec61883 libmad libmp4v2 libmpeg2 mjpegtools mpg123 twolame xvidcore libquicktime sox libopusenc opus opus-tools opusfile schroedinger aom celt flac libde265 opencore-amr openjpeg2 speex libfishsound gst-plugins-base gst-plugins-base-libs gst-plugins-good gstreamer libcanberra-gstreamer fmt atomicparsley
```

<br>

### To make your computer much faster, more efficient, more secure. Increase performance and FPS in GAMES: 
#### Read. In the line below contains `13` command lines, obey each command:
1. - `echo -e "vm.swappiness=10" > /etc/sysctl.conf`
2. - `echo -e "net.ipv4.conf.all.rp_filter=1" >> /etc/sysctl.conf`
3. - `echo -e "net.ipv4.tcp_syncookies=1" >> /etc/sysctl.conf`
4. - `echo -e "net.ipv4.ip_forward=1" >> /etc/sysctl.conf`
5. - `echo -e "net.ipv4.tcp_dsack=0" > /etc/sysctl.conf`
6. - `echo -e "net.ipv4.tcp_sack=0" > /etc/sysctl.conf`
7. - `echo -e "fs.file-max=100000" > /etc/sysctl.conf`
8. - `echo -e "kernel.sched_migration_cost_ns=5000000" > /etc/sysctl.conf`
9. - `echo -e "kernel.sched_autogroup_enabled=0" > /etc/sysctl.conf`
10. - `echo -e "vm.dirty_background_bytes=16777216" > /etc/sysctl.conf`
11. - `echo -e "vm.dirty_bytes=50331648" > /etc/sysctl.conf`
12. - `echo -e "kernel.pid_max=4194304" > /etc/sysctl.conf`
13. - `echo -e "vm.oom_kill_allocating_task=1" > /etc/sysctl.conf`

<br>

### In `/etc/security/limits.conf` Note: Below command will increase performance and FPS in games. 
##### Read. In the line below contains `11` command lines, obey each command: 
1. - `echo -e "hard stack unlimited" >> /etc/security/limits.conf`
2. - `echo -e "nproc unlimited" >> /etc/security/limits.conf`
3. - `echo -e "nofile 1048576" >> /etc/security/limits.conf`
4. - `echo -e "memlock unlimited" >> /etc/security/limits.conf`
5. - `echo -e "as unlimited" >> /etc/security/limits.conf`
6. - `echo -e "cpu unlimited" >> /etc/security/limits.conf`
7. - `echo -e "fsize unlimited" >> /etc/security/limits.conf`
8. - `echo -e "memlock unlimited" >> /etc/security/limits.conf`
9. - `echo -e "msgqueue unlimited" >> /etc/security/limits.conf`
10. - `echo -e "locks unlimited" >> /etc/security/limits.conf`
11. - `echo -e "* hard nofile 1048576" >> /etc/security/limits.conf`

<br>

### include in `/etc/systemd/`
##### Read. In the line below contains `3` command lines, obey each command: 
1. - `cd /etc/systemd/`
2. - `wget https://raw.githubusercontent.com/felipefacundes/desktop/master/etc-systemd/system.conf`
3. - `wget https://raw.githubusercontent.com/felipefacundes/desktop/master/etc-systemd/user.conf`

<br>

### For notebooks:
##### Read. In the line below contains `2` command lines, obey each command: 

1. - `pacman -S xf86-input-synaptics acpi libinput`
2. - `echo -e "vm.laptop_mode=1" >> /etc/sysctl.conf`

<br>

### If you don't enjoy your monitor, turning off, or dimming the screen (black screen), do the following: 
##### Read. In the line below contains `2` command lines, obey each command: 
1. - `cd /etc/X11/xorg.conf.d/`
2. - `wget https://raw.githubusercontent.com/felipefacundes/desktop/master/Arch_linux_Install/arch_linux_install_scripts/naodesligamonitor.conf`

<br>

---
## Step 08 - Enable Hibernation
---
<br>

#### For system HIBERNATION. Example: 
##### Read. In the line below contains `2` command lines, obey each command:
1. - `blkid`
2. - `nano /etc/default/grub`

<br>

#### In resume=UUID change according to the example below and place according to the result of the blkid command, only the part of the UUID 
`resume=UUID="swap UUID" em "GRUB_CMDLINE_LINUX_DEFAULT="`

<br>

#### In `/etc/mkinitcpio.conf` include in `"HOOKS="` `"resume"` right after "filesystems" 
#### After everything changed, run the commands for the system to go to sleep: 
##### Read. In the line below contains `2` command lines, obey each command: 
1. - `grub-mkconfig -o /boot/grub/grub.cfg`
2. - `mkinitcpio -P`

<br>

#### If you use paging swapfile - file to virtual memory (swapfile) and want the system to hibernate, follow the tutorial below: 

[https://github.com/felipefacundes/desktop/tree/master/swapfile-hibernate](https://github.com/felipefacundes/desktop/tree/master/swapfile-hibernate)

<br>

---
## Step 09 - Additional Apps, Drivers and Configs: 
---
<br>

### To install to printers: 
##### Read. In the line below contains `3` command lines, obey each command:
1. - `pacman -S cups cups-filters cups-pdf cups-pk-helper libcups python-pycups python2-pycups system-config-printer lib32-libcups splix foomatic-db foomatic-db-engine foomatic-db-gutenprint-ppds foomatic-db-nonfree foomatic-db-nonfree-ppds foomatic-db-ppds hplip`
2. - `systemctl enable cups-browsed.service`
3. - `systemctl enable org.cups.cupsd.service`

<br>

### Totally optional, for virtualbox run: 
`pacman -S virtualbox-host-modules-arch virtualbox-guest-iso virtualbox`

<br>

### To install LibreOffice: 
`pacman -S libreoffice-fresh libreoffice-fresh-pt-br`

<br>

### Optional. To install TrueType fonts to increase the number of fonts on your system, search and install the ones you prefer: 
`pacman -Ss ttf`

<br>

#### If you prefer to install all the fonts available in the repository, all at once, without even searching: 
##### Read. In the line below contains `2` command lines, obey each command: 
1. - `pacman -S $(pacman -Ssq ttf)`
2. - `fc-cache`

<br>

#### Or you can also install these fonts:
##### Read. In the line below contains `2` command lines, obey each command: 

1. - `pacman -S wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei terminus-font tamsyn-font dina-font adobe-source-han-sans-otc-fonts noto-fonts-emoji noto-fonts-cjk gnu-free-fonts font-bitstream-speedo bdf-unifont adobe-source-code-pro-fonts adobe-source-sans-pro-fonts adobe-source-serif-pro-fonts`
2. - `fc-cache`

<br>

##### To have excellent accessibility support like TTS, follow the tutorial on my website: 

[https://brasiltts.wordpress.com/](https://brasiltts.wordpress.com/)

<br>

##### For you to change the name of your distribution, if you want: 
1. - `pacman -S lsb-release`
2. - `nano /etc/lsb-release`

<br>

### So that you can use the famous AUR repository, when you don't have the desired program in the official repository, install yay, to use the AUR repository: 

##### Read. In the line below contains `3` command lines, obey each command: 
1. - `git clone https://aur.archlinux.org/yay.git`
2. - `cd yay`
3. - `makepkg -si`

<br>

### For you who came from UBUNTU or DEBIAN, and are used to the apt-get command, use: 
`bash <(curl -s https://raw.githubusercontent.com/felipefacundes/apt-get-pacman/master/iniciorapido.sh)`

<br>

### If you want to install Windows Games on Linux with ease. See the PlayOnGit project 
- [https://www.github.com/felipefacundes/PlayOnGit/](https://www.github.com/felipefacundes/PlayOnGit/)
- [https://jogoslinux.github.io/](https://jogoslinux.github.io/)

#

#### For obsolete driver incompatible with current kernels: Catalyst 

#### DO NOT use it, it's deprecated, it's here for Linux history purposes.

##### pacman-key -r 653C3094
##### pacman-key --lsign-key 653C3094
##### echo # uninstall the open drivers:
##### pacman -Rcc lib32-ati-dri ati-dri xf86-video-ati
##### pacman -S catalyst-hook catalyst-libgl catalyst-utils acpid qt4
##### install extra components (optional but needed for gaming):
##### pacman -S opencl-catalyst lib32-catalyst-utils lib32-catalyst-libgl lib32-opencl-catalyst

#

<br/>
<br/>
<br/>
<br/>
<br/>

```bash
                    ,cldxOxoc:;,
               ,;:okKNXKK0kO0Okxddol:;,
        ,;codxkkOKXKko:'......,;clx0KXXOxol:,
   :lodxxdololc:,'..................,cdk00kxkOkxoc,
  ;XOxdl:,..  .......................... ..,;lx0XNx
  lX0c      ..;dddddooooollll,............     .0XO'
  oKK;    ....0NNXXKKK00OOOkx'.............     x0x,
  lXX;   ....,WWNNNKdddoooooc.'..............   okk,
  cXW:  .....xWWWWWl''''''''''''''............  dd0,
  :OWo ......NMMMMWOkkkkxxxc''''''''.......... .kxK'
  ,dKk .....lWWWWMMMWWWNNNX:,''''''''...........KOx'
  ,lk0......ONNNWWddddddddo,,,,,''''''.........lNOc'
  'cdk;....;KXXXNO''',,,,,,,ddddoooolllccc:....0WO,'
  ',ddd....o0KKKXl'',,,,,,,lWWNNNXXKKK00OOc...,0Xx'
   'llo;...kO00K0,'',,,,,,,0MWWWN0OOOkkkxx'...oxK;'
   ',xcl..,lloool''',,,,,,:WWMMMX''''''......cxko'
    ':x:c......''''''',,,,xNWWWM0dddddool...:00O,'
     'ld::.......'''''',,,KNNNWWWMMMWWWWx..;XXK;'
      'dd::.......'''''''lKXXNN0kkOOOkkk;.;0KK;'
       'ox::........'''''x0KKKX;'''......lK0k;'
        'cx:c,........'.,OO00KO........'kN0o,'
         ',dllc'........lkkOO0c.......:KNk:'
           ':lldc.......oodddd'.....,d0Oc,'
            ',:ldxl'..............'lxxo;,'
              ',;lx0x;..........,cldo:'
               '',cxXXd,....,cdxkd:'
                   ',;o0N0odkkkk,'
                     ',;lxdl:,'

 Tutorial by:
 ___    _             ___                     _
| __|__| (_)_ __  ___| __|_ _ __ _  _ _ _  __| |___ ___
| _/ -_) | | '_ \/ -_) _/ _` / _| || | ' \/ _` / -_|_-<
|_|\___|_|_| .__/\___|_|\__,_\__|\_,_|_||_\__,_\___/__/
           |_|

┏┓
┃┃╱╲ nesta
┃╱╱╲╲ casa
╱╱╭╮╲╲ todos
▔▏┗┛▕▔ usam
╱▔▔▔▔▔▔▔▔▔▔╲
LINUX
╱╱┏┳┓╭╮┏┳┓ ╲╲
▔▏┗┻┛┃┃┗┻┛▕▔
--------------------------
```
