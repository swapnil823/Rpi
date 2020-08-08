# Rpi Setup

- Download raspbian lite

[Download Raspberry Pi OS for Raspberry Pi](https://www.raspberrypi.org/downloads/raspberry-pi-os/)

- Write Image to SDCARD, Replace x with image name

```bash
dd bs=4M if=xxxx.img of=/dev/sdb status=progress conv=fsync
```

- Enable ssh and WiFi, change SSID and PASS as required

```bash
mkdir /mnt/boot
mount /dev/sdb1 /mnt/boot
#Store wifi config
echo "ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
ssid="XXXXSSID"
psk="XXXXSSID"
proto=RSN
key_mgmt=WPA-PSK
pairwise=CCMP
auth_alg=OPEN
}" > /mnt/boot/wpa_supplicant.conf
#Enable ssh
touch /mnt/boot/ssh
```

- Insert SDCARD and boot RPi
- Do a quick scan using an IP scanner such as FING to find out RPi's IP address
- ssh to RPi
- Update index and packages

```bash
sudo apt-get update
sudo apt-get upgrade
```

- Make root read only so as to avoid sdcard corruption on power loss

[Read-Only Raspberry Pi](https://learn.adafruit.com/read-only-raspberry-pi/)

```bash
wget https://raw.githubusercontent.com/adafruit/Raspberry-Pi-Installer-Scripts/master/read-only-fs.sh
sudo bash read-only-fs.sh
```

- Run Mount to confirm RO filesystem

> pi@raspberrypi:~ $ mount
/dev/mmcblk0p2 on / type ext4 (ro,noatime)

- Finally create some alias to help remount rw in /etc/bash.bashrc by appending following

```bash
set_bash_prompt() {
fs_mode=$(mount | sed -n -e "s/^\/dev\/.* on \/ .*(\(r[w|o]\).*/\1/p")
PS1='\[\033[01;32m\]\u@\h${fs_mode:+($fs_mode)}\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
}
alias ro='sudo mount -o remount,ro / ; sudo mount -o remount,ro /boot'
alias rw='sudo mount -o remount,rw / ; sudo mount -o remount,rw /boot'
PROMPT_COMMAND=set_bash_prompt
```

- Add following to ~/.bash_logout to ensure we go back to read only after logout

```bash
mount -o remount,ro /
mount -o remount,ro /boot
```

### HAPPY HACKING !
