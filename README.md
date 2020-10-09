# How to install DEXIS in Linux Using VirtualBox

A tutorial on how to setup DEXIS to work on Linux using a Virtual machine.

## Disclaimer

This is an unofficial guide and is not supported or sponsored by KaVo Kerr:tm:. These instructions are purely for educational purposes and as such, do not come with any type of warranty. Use at your own risk.

KaVo:tm:, DTX Studio:tm:, DEXIS:tm:, Gendex:tm:, i-CAT:tm:, Soredex:tm:, Instrumentarium:tm:, and any logos thereof are all owned by KaVo Kerr under the Envista Holdings Corp portfolio.  
I do not own, nor will attempt to profit from the brands mentioned above.

## Before you start

I wrote this with Fedora Linux in mind. The commands may be different depending on your distribution. On Fedora you need to enable [RPM Fusion](https://rpmfusion.org/) to get VirtualBox in the repos.

## Connect to the network share

The database is shared out via SMB/CIFS Windows sharing, and needs to be configured to connect at startup.  

**I realized that you can use the standard windows sharing in the Virtual Machine assuming you are using a Bridged Network, instead of mounting the share in Linux.

### Create a folder to use as the local mount point (Similar to a mapped drive in Windows)

``mkdir /media/data``

### Edit the fstab file so that the proper share and proper credentials are saved

1. ``sudo gedit /etc/fstab``

2. Add  the following to the end of the file.  

        //<server-name>/<share-name> : /media/data cifs : domain=<domain-name>,username=<domain-user>,password=<user-password> : 0 : 0
        
    Replacing the variables to match the current environment. The domain section can be omitted if installing on a workgroup. The colon is representative of a tab character. There are no colons in the actual file. Note that forward slashes are used. Linux doesn't use backslash \ .  
    An example would be:  

        //server/data   /media/data cifs    username=admin,password=password    0   0

3. Reboot the system. and check if your share is mounted properly: ``ls /media/data``  
This should output the list of files and directories of the data folder.

## Install VirtualBox and setup Windows 10

This is not a tutorial on how to install and configure the initial setup for VirtualBox. Once you have this configured and setup with Windows 10, you can continue. Make sure you also install the Guest additions and the vbox extension pack.

### Configure advanced VirtualBox settings

There are a few extra steps that we need to do before we are able to use the devices and connect to the server.

1. Add the current user to the vboxusers group. ``sudo usermod -a -G vboxusers <username>``
2. Map the share in VirtualBox: Go to settings>>shared folders and click the plus sign on right to add a folder:  Click the dropdown by folder path and select other.  
Browse to ``/media/data`` and select it as the path
3. Select the automount checkbox, and for the mount point put ``Z:\`` or whatever letter you desire
4. Once loaded in the VM, there should be a Z drive in This PC. If not, the UNC path to the “server” is ``\\vboxsvr\``
5. Restart the computer again so the usermod changes can go into effect.

### Setup the VM to use DEXIS

Now we should be able to install DEXIS as normal. In the Virtual Machine Go through the networked drive/unc path and run the installation. Make sure you set the datapath during the installation.

DEXIS should be able to view xrays like normal.

## Enable USB Passthrough to access the hardware

Make sure the VM is powered off prior to making these changes.

1. Go to Settings>>USB.
2. Enable USB 2.0
3. Click the add USB Filter at the bottom (green "+") and select the device that is plugged in.
4. After the device is selected in the filter list, you should be able to power on the VM and the device should appear in Device Manager assuming the proper drivers are installed on the Virtual Machine.

**Note**  
The device needs to first be visible by Linux for it to appear in the USB device list.  
Titanium:tm: and KaVo IXS:tm: Sensors, DEXcam:tm: 4HD, and CariVu:tm: all have signed drivers and will appear without issue.  
Platinum:tm: Sensors require proprietary drivers, and ``ndiswrapper`` to install on Linux. For legal reasons I cannot provide those drivers.
