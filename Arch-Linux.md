# Arch Linux

Available at [archlinux.org](https://archlinux.org/), Arch Linux provides one of
the best Linux wikis available.  There is also a package search database
available at the same site.

## Quick Install to XFCE
(Last updated March 2022 using the 2022.03.01 installation medium.)

Follow the [installation guide](https://wiki.archlinux.org/title/Installation_guide)
but also install other materials before rebooting.  These suggestions get a
decent workstation using the XFCE desktop environment.

Install the following packages and their dependencies using `pacman -S` or even
as part of the `pacstrap` step:
* `util-linux` (includes `fstrim.timer` for use with SSD)
* extra package management commands `pacman-contrib`
* Intel or AMD microcode for CPU as appropriate (`intel-ucode` or `amd-ucode`)
* Grub2 Bootloader `grub`
* DHCP client `dhcpcd`
* OpenSSH client and server `openssh`
* X server `xorg-server`
* appropriate video driver
  * Example:  `xf86-video-intel` and `mesa`
  * see [Xorg#Driver_Installation](https://wiki.archlinux.org/title/Xorg#Driver_installation)
* display manager `lightdm` (and `lightdm-gtk-greeter`)
* audio manager `pulseaudio` and optionally `pavucontrol` (expected by XFCE)
* XFCE `xfce4 xfce4-goodies`
  * file and volume management `gvfs` (`gvfs-mtp` for Android phones)
  * Network Manager `networkmanager` `network-manager-applet`
* at least one font `ttf-dejavu`
* web browser `firefox`
* useful tools like `git`, `mc`, `tmux`, and `vim`

Set up a sudoers group and at least one non-root user.
* `groupadd -r sudo`
* In `/etc/sudoers` uncomment or add the line `%sudo  ALL=(ALL:ALL) ALL`
* Add the new user with `useradd -m -G sudo -s /bin/bash USERNAME`
* `passwd USERNAME` to change new user's password

Make sure to generate the latest configuration
for Grub:  `grub-mkconfig -o /boot/grub/grub.cfg`

At this point, reboot into the Arch environment.  Enable LightDM with `systemctl
enable lightdm` and either reboot or start them with `systemctl start lightdm`.

If everything is correct, LightDM should appear automatically (or it may need to
be enabled with `systemctl started lightdm`).  Also, some timers may need to be
enabled:
* `systemctl enable paccache.timer` (cleans package cache in
  `/var/cache/pacman/pkg/`)
* `systemctl enable fstrim.timer` (applies trim periodically if using SSD)
