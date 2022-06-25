# MSI GS66 12UGS (2022) for Data Science

Here is how I setup my MSI GS66 (Early 2022, RTX 3070 Ti) to work with Ubuntu.

- [Caveats](#caveats)
   - [Wifi](#wifi)
   - [Webcam](#webcam)
- [Installation](#installation)
   - [Prereqs](#prereqs)
   - [BIOS](#bios)
   - [Install](#install)
   - [First Boot](#first-boot)
   - [First Login](#first-login)
   - [Second Login](#second-login)
   - [Final Reboot](#final-reboot)
- [Thoughts](#thoughts)
- [Notes](#notes)
 
# Caveats
I do a full disk install. If I ever do need to use Windows I have found installing and booting from an external SSD to be a simple solution. I am currently using Ubuntu 20.04 as I found 22.04 to be unstable on this laptop (sporadic hangs).
## Wifi
I do not have the onboard wifi working. Instead I use a USB adapter (Edimax). I expect the drivers could be loaded manually, but I prefer to simply wait and hope they get included in a kernel update.
## Webcam
I do not have the on-board webcam working. Instead I use a USB webcam. Again, it would probably be possible to get this working given time.

# Installation
## Prereqs
   - Make and insert:
      - Bootable usb stick 20.04.3
      - Wifi adapter (Edimax)
## BIOS
   - Power on and repeated press delete to get into BIOS
   - BIOS settings:
      - Security->Secure Boot->Secure Boot Support [Disabled]
      - Boot->Boot Option #1->[USB CD/DVD:UEFI: ...your usb... ]
   - Save & Exit->Save Changes and Reset->Yes
## Install
   - Select "Ubuntu*" (or wait, as it selects it by default)
   - Install Ubuntu
      - Do not setup a network
      - Normal (or Minimal) Installation
      - Install third-party software...
      - Erase disk and install Ubuntu
      - Set timezone and account details
      - Wait for install and restart
      - When restart stops (flashing cursor), power off by holding power button
## First Boot
   - Once powered off, remove the USB stick
   - Power on and repeatedly press ESC to get into GRUB
   - At the grub prompt enter "normal"
   - Immediately press ESC one more time.
   - Highlight "*Ubuntu" and press e
   - At the end of the line starting linux (i.e. after $vt_handoff) add:
```
nouveau.modeset=0
```
   - Press f10 and wait for reboot
## First Login
Update
   - Login and setup wifi (using Edimax dongle)
   - Then, open a command prompt and type:
```
sudo apt update
sudo apt upgrade
sudo reboot
```
## Second Login
Install NVIDIA Driver and CUDA
   - Interupt the boot as above[#first login] and apply the modeset fix again
   - Download and install cuda_11.7.0_515.43.04_linux.run
```
wget https://developer.download.nvidia.com/compute/cuda/11.7.0/local_installers/cuda_11.7.0_515.43.04_linux.run
sudo apt install build-essential
sudo sh cuda_11.7.0_515.43.04_linux.run
```
   - Edit your .bashrc and add
```
export PATH="/usr/local/cuda-11.7/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda-11.7/lib64:$LD_LIBRARY_PATH"
```
   - Finish updating
```
sudo apt autoremove
sudo apt dist-upgrade
```
   - Optionally, make grub remember the last booted kernal. Edit /etc/default/grub and add
```
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
```
   - And update the grub configuration
```
sudo update-grub
```
## Final Reboot
```
sudo reboot
```
- There is no need for the modeset fix anymore.
- The Desktop should work
- The NVIDIA driver should be working too (on-demand)

# Thoughts
```
Couple of things I noted if you run prime-select (untested!):

1) It only seems to update the most recent kernel, you may need to edit to update the kernel you are using, e.g.:
sudo vi $(which prime-select)
   proc = subprocess.Popen(['update-initramfs','-u','-k','5.11.0-27-generic'],stdout=subprocess.PIPE)

2) prime-select on-demand
Will create a file that prevents the gui from starting, remove it:
rm /lib/modprobe.d/nvidia-kms.conf
or edit prime-select so it doesn't get made, e.g.
def _write_kms_settings(self, value):
   return
   ...
```
# Notes
```
Usual additional setup should now work, e.g.

Install cuDNN 11.x (requires login)
https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html

Install docker
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html

NVIDIA on-demand run with GPU 
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears
```
