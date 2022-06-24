```
Setup-GS66-12UGS
Setup notes for MSI GS66 Laptop

(Fo Wifi I use an Edimax dongle, but perhaps backports will work before the kernel updates handle it)

Make bootable usb 20.04.3
Insert usb stick
Insert Edimax Wifi dongle
Power on and repeated press delete to get into Bios
Bios settings:
   Security->Secure Boot->Secure Boot Support [Disabled]
   Boot->Boot Option #1->[USB CD/DVD:UEFI: ...your usb... ]
Save & Exit->Save Changes and Reset->Yes

Ubuntu*
Install Ubuntu
Do not setup a network
Normal (or Minimal) Installation
Install third-party software...
Erase disk and install Ubuntu
Set timezone and account details
Wait for install and restart
When restart stops (flashing cursor), power off by holding power button

Remove USB stick
Power on and repeatedly press ESC to get into GRUB
At the grub prompt enter "normal" and,
immediately press ESC one more time.
*Ubuntu - Press e
At the end of the line starting linux (i.e. after $vt_handoff) add
nouveau.modeset=0
Press f10 and wait for reboot

Login and setup wifi (using Edimax dongle)

Then:
sudo apt update
sudo apt upgrade
sudo reboot, interupt, modeset=0; build-essential; cuda install defaults ---

-???
sudo apt autoremove
sudo apt dist-upgrade

Optionally, edit /etc/default/grub and add
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
And run:
sudo update-grub

Then:
sudo reboot (interupt the boot as above and apply the modeset fix again)

Download cuda_11.7.0_515.43.04_linux.run
sudo apt install build-essential
sudo sh cuda_11.6.0_515.43.04_linux.run

Edit your .bashrc and add
export PATH="/usr/local/cuda-11.7/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda-11.7/lib64:$LD_LIBRARY_PATH"
sudo reboot (no need for modeset fix anymore)

gui should work - nvidia-smi should work
```
---
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
---
```
Usual setup should now work, e.g.

Install CUDA 11.7
DO NOT allow the driver to be updated (deselect during config)
wget https://developer.download.nvidia.com/compute/cuda/11.7.0/local_installers/cuda_11.7.0_515.43.04_linux.run
sudo sh cuda_11.7.0_515.43.04_linux.run

Edit your .bashrc and add
export PATH="/usr/local/cuda-11.7/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda-11.7/lib64:$LD_LIBRARY_PATH"

Install cuDNN 11.x (requires login)
https://developer.nvidia.com/compute/cudnn/secure/8.4.1/local_installers/11.6/cudnn-linux-x86_64-8.4.1.50_cuda11.6-archive.tar.xz
# tar -xvf cudnn-11.3-linux-x64-v8.2.0.53.tgz
sudo cp -P cuda/lib64/* /usr/local/cuda/lib64/
sudo cp -P cuda/include/* /usr/local/cuda/include/
sudo chmod a+r /usr/local/cuda/include/cudnn.h
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*

Install docker
https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html
```
