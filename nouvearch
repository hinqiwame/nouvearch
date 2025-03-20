#!/bin/bash

# Function to display messages
msg() {
    echo -e "\e[1;32m$1\e[0m"
}

# Function to display error messages
err() {
    echo -e "\e[1;31m$1\e[0m" >&2
}

# Check if running as root
if [[ $EUID -ne 0 ]]; then
   err "This script must be run as root. Use sudo or switch to the root user."
   exit 1
fi

msg "Updating the system..."
pacman -Syu --noconfirm

msg "Removing proprietary NVIDIA drivers if they exist..."
pacman -Rs --noconfirm nvidia nvidia-dkms nvidia-utils lib32-nvidia-utils

msg "Installing Nouveau drivers and necessary packages..."
pacman -S --noconfirm mesa lib32-mesa xf86-video-nouveau

msg "Checking for NVIDIA driver blacklists..."
BLACKLIST_FILES=(
    "/etc/modprobe.d/nouveau_blacklist.conf"
    "/usr/lib/modprobe.d/nvidia-utils.conf"
    "/usr/lib/modules-load.d/nvidia-utils.conf"
    "/usr/lib/udev/rules.d/60-nvidia.rules"
)

for file in "${BLACKLIST_FILES[@]}"; do
    if [[ -f $file ]]; then
        msg "Found $file. Commenting out any lines that blacklist Nouveau..."
        sed -i 's/^\s*blacklist\s*nouveau/#&/' "$file"
    fi
done

msg "Updating initramfs..."
mkinitcpio -P

msg "All done! Reboot your system to apply the changes."
