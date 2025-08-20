[![Releases](https://img.shields.io/github/v/release/achrafkm22/artix-install?color=2188FF&label=Releases&logo=github)](https://github.com/achrafkm22/artix-install/releases)

# Artix Install — Minimal Artix Linux Installer Script (systemd-free)

![Tux Linux](https://upload.wikimedia.org/wikipedia/commons/3/35/Tux.svg)

Basic Artix installation script that guides a local install from an existing live environment. It targets systemd-free setups and offers choices for dinit, OpenRC, runit and s6. The script keeps defaults conservative and stays easy to adapt.

Tags: artix, artix-linux, artixlinux, bash-script, dinit, init-system, installer-script, linux, linux-installer, nosystemd, open-source, openrc, runit, s6, shell-script, systemd-free

- Repository: artix-install
- Short description: Basic Artix installation script
- Releases: https://github.com/achrafkm22/artix-install/releases

---

Table of contents

- Features
- Quick facts
- Requirements
- Download and run
- Example run
- Config options
- Init system choices
- Partition schemes
- Network and mirrors
- Post-install tasks
- Troubleshooting
- Contributing
- License

---

Features

- Simple, script-driven installer for Artix Linux.
- Works from an Artix or Arch live environment.
- Choose between dinit, OpenRC, runit, s6.
- Supports UEFI and legacy BIOS.
- LUKS and LVM support.
- Filesystem choices: ext4, btrfs, xfs, f2fs.
- Configurable packages and desktop environments.
- Sets up a minimal, bootable system ready for user setup.
- Logs each major step for easier debugging.

Quick facts

- Language: Bash
- Type: Shell installer script
- Target: Systemd-free Artix installs
- Tested on: Artix live environment and Arch live environment
- Releases and installer files: https://github.com/achrafkm22/artix-install/releases

Requirements

- A working live environment with internet.
- Basic familiarity with Linux partitions and mounting.
- A target disk or virtual disk ready for installation.
- Root privileges in the live environment.
- Optional: EFI firmware for UEFI boot.

Download and run

The release page contains the script file as a downloadable asset. Download the release file and execute it on the live system. For example:

1. Visit the Releases page and pick the latest release:
   https://github.com/achrafkm22/artix-install/releases

2. Download the installer asset (example file name: artix-install.sh).

3. Make it executable and run it:

```bash
curl -LO <release-asset-url>
chmod +x artix-install.sh
sudo ./artix-install.sh
```

The script runs in an interactive mode. It asks for the target disk, partition layout, and init choice. It performs base installation tasks and sets up the bootloader.

Example run

These example steps show a simple UEFI install on /dev/sda. Adjust them for your layout.

```bash
# boot the live iso, become root or use sudo
lsblk            # verify target disk (e.g., /dev/sda)
# download the release asset from the Releases page
curl -LO https://github.com/achrafkm22/artix-install/releases/download/v1.0.0/artix-install.sh
chmod +x artix-install.sh
sudo ./artix-install.sh
```

During the run choose:
- Disk: /dev/sda
- UEFI: yes
- Root FS: ext4
- Init system: runit
- Bootloader: grub or systemd-boot (script offers appropriate boot setup)

The script formats partitions and installs base packages. It sets up locale, timezone, and a default user. It also includes hooks for extra packages.

Config options

The script uses a small config file or runtime prompts. You can set these values before running to avoid prompts:

- TARGET_DISK=/dev/sda
- EFI_PART=/dev/sda1
- ROOT_PART=/dev/sda2
- SWAP_SIZE=4G
- FS_ROOT=ext4
- INIT_SYSTEM=openrc
- HOSTNAME=artixbox
- USERNAME=user
- PASSWORD=changeme
- EXTRA_PACKAGES="nano sudo openssh"

Place these variables in a file called artix-config.env and export them before running:

```bash
source ./artix-config.env
sudo ./artix-install.sh
```

Init system choices

The script supports these init systems:

- dinit — lightweight, modern init.
- OpenRC — traditional BSD-style init used by many Artix users.
- runit — simple and fast supervise-based init.
- s6 — process supervision with modular design.

Each option installs the proper service manager, sets default services, and includes sample service units. The script warns when a service needs manual tuning.

Partition schemes

The script supports multiple partition schemes. Use interactive prompts or preseed a layout in the config.

Common layouts

- UEFI (GPT)
  - /dev/sda1 — EFI System Partition (500M, FAT32)
  - /dev/sda2 — root (ext4, btrfs, xfs)
  - /dev/sda3 — swap (optional)

- Legacy BIOS (MBR)
  - /dev/sda1 — root
  - /dev/sda2 — swap

- LUKS + LVM
  - /dev/sda1 — EFI or /boot
  - /dev/sda2 — LUKS container -> LVM -> logical volumes (root, home, swap)

The script can format /boot separately when required by the chosen bootloader.

Network and mirrors

The script fetches packages with pacman or the Artix equivalent. It uses the runtime network. For faster installs, adjust the mirrorlist. The script offers an option to auto-rank mirrors with reflect or to use a local mirror.

If you use wifi, run the live environment tools first to connect, or choose to use the script's network hooks which call iwctl or wpa_supplicant.

Post-install tasks

After the script finishes it runs basic post-install tasks:

- sets root password
- creates a user with sudo rules
- sets hostname and locale
- installs chosen desktop packages or window manager
- enables network and common services
- installs and configures bootloader

You must chroot into the installed system for manual tweaks if you want custom kernels, custom modules, or encrypted boot.

Troubleshooting

- The script logs to /var/log/artix-install.log on the live system. Review it for error details.
- If pacman fails, check the mirrorlist and DNS.
- If the bootloader fails, verify the EFI partition flags and mount points.
- If system fails to boot, use the live environment to mount the root and inspect /etc/mkinitcpio.conf and fstab.

Common fixes

- Rebuild initramfs if kernel or hooks changed.
- Reinstall bootloader from chroot and run grub-install or bootctl.
- Ensure the correct init system packages are in /etc/mkinitcpio.conf or initramfs hooks.

Security and best practices

- Use a strong password for root and user accounts.
- For encrypted systems, back up the LUKS header.
- Keep a live USB handy in case of kernel or boot issues.
- Review post-install logs before first reboot.

Automation and CI

The script supports basic automation via environment variables. For advanced automation, use a configuration management tool or extend the script to accept a full JSON/YAML preset.

Extending the script

- Add custom package lists.
- Add hooks for desktop environments like XFCE, KDE, GNOME.
- Add support for other bootloaders.
- Add preseed templates for cloud or VM images.

Contributing

- Fork the repo.
- Open a feature branch with descriptive commits.
- Add tests for new logic where possible.
- Open a pull request and describe the changes and test steps.
- Use issue templates for bugs or feature requests.

Release assets and downloads

The release page hosts versioned assets. Download the script file from the release and run it. See the Releases page here:
https://github.com/achrafkm22/artix-install/releases

Badges and status

[![Releases](https://img.shields.io/github/v/release/achrafkm22/artix-install?color=2188FF&label=Releases&logo=github)](https://github.com/achrafkm22/artix-install/releases) [![Bash](https://img.shields.io/badge/language-Bash-4EAA25.svg)](https://github.com/achrafkm22/artix-install)

Screenshots and sample output

![Console install example](https://upload.wikimedia.org/wikipedia/commons/3/35/Tux.svg)

License

This project uses the MIT License. See the LICENSE file for details.

Contact

- Issues: open issues on the repository
- Pull requests: submit PRs for fixes and features

References and resources

- Artix Linux: https://artixlinux.org
- Artix init guides: consult the Artix wiki and community pages
- LUKS and LVM docs: official cryptsetup and lvm documentation

Acknowledgments

- Artix community for guidance on init systems.
- Open source projects that provide the base tools used by this script.