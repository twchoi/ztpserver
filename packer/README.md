#ZTPServer Setup - Packer.io VM Automation

##Introduction
You can use Packer.io to automate the creation of the ZTPServer VM.  By using this method, you can be sure that all of the required packages and dependencies are installed right out of the gate.  This procedure will:

* Create a VM with 7GB Hard Drive
* 2GB RAM
* Fedora 20 Minimal Install
* Python 2.7.5 with PIP
* Hostname ztps.ztps-test.com
    * eth0 (vmnet8-NAT) 172.16.130.10
    * eth1 (vmnet1-Bridged) DHCP
* Users
    * root/eosplus and ztpsadmin/eosplus
* DHCP installed with Option 66/67 configured
* BIND DNS server installed with zone ztps-test.com
    * wildcard forward rule to 8.8.8.8 for all other queries
    * SRV RR for im.ztps-test.com
* rsyslog-ng installed
* XMPP server configured for im.ztps-test.com
* httpd installed and configured for ZTPServer (mod_wsgi)
* ZTPServer installed

##Installation of Packer
> **Note:** This installation requires internet access.

Packer.io automates the creation of the Virtual Machine.  Therefore, the first step is downloading and installing Packer.

1. Download the appropriate binaries - http://www.packer.io/downloads.html
2. Unzip and move to desired location eg ~/packer or /usr/local/share/ or /usr/local/bin/
3. Set ENV variable (or just put Packer somewhere the ```PATH``` is already pointing - ```echo $PATH```)
    * EG: in ~/.bash_login, add ```PATH=$PATH:/path/to/packer/files```
4. Run ```packer``` to make sure ```PATH``` is updated.

##Creating a VM for use with VMWare Fusion
> **Note:** The following procedure was tested using VMWare Fusion 6.0.3.

1. Retrieve the EOS+ packer files here: 
2. ```cd``` to the location of the .json
3. Run ```packer build ztps-fedora_20_x86_64.json```
    You will see:
    ```
    phil:ztpserver phil$ packer build ztps-fedora_20_x86_64.json
    vmware-iso output will be in this color.
    
    ==> vmware-iso: Downloading or copying ISO
    vmware-iso: Downloading or copying: http://mirrors.xmission.com/fedora/linux/releases/20/Fedora/x86_64/iso/Fedora-20-x86_64-netinst.iso
    ==> vmware-iso: Creating virtual machine disk
    ==> vmware-iso: Building and writing VMX file
    ==> vmware-iso: Starting HTTP server on port 8608
    ==> vmware-iso: Starting virtual machine...
    ==> vmware-iso: Waiting 2s for boot...
    ==> vmware-iso: Connecting to VM via VNC
    ==> vmware-iso: Typing the boot command over VNC...
    ==> vmware-iso: Waiting for SSH to become available...
    ```
4. Once the ISO is downloaded, packer brings up a VMWare VM. The Anaconda installation will proceed without any user input.
5. After 10 minutes the OS installation will be complete, the VM will reboot, and you will be presented with a login prompt.  Resist the urge to log in and tinker - things are still being setup.
6. You'll notice the packer builder ```ssh``` into the VM and begin working on updating, installing and configuring new services.
    ```
    phil:ztpserver phil$ packer build ztps-fedora_20_x86_64.json
    vmware-iso output will be in this color.
    
    ==> vmware-iso: Downloading or copying ISO
    vmware-iso: Downloading or copying: http://mirrors.xmission.com/fedora/linux/releases/20/Fedora/x86_64/iso/Fedora-20-x86_64-netinst.iso
    ==> vmware-iso: Creating virtual machine disk
    ==> vmware-iso: Building and writing VMX file
    ==> vmware-iso: Starting HTTP server on port 8574
    ==> vmware-iso: Starting virtual machine...
    ==> vmware-iso: Waiting 2s for boot...
    ==> vmware-iso: Connecting to VM via VNC
    ==> vmware-iso: Typing the boot command over VNC...
    ==> vmware-iso: Waiting for SSH to become available...
    ==> vmware-iso: Connected to SSH!
    ==> vmware-iso: Uploading the 'linux' VMware Tools
    ==> vmware-iso: Uploading conf => /tmp/packer
    ==> vmware-iso: Provisioning with shell script: scripts/setup.sh
    ... (shell script output)
    ```
7. After some extensive yumming, you will see:
    ```
    ==> vmware-iso: Gracefully halting virtual machine...
        vmware-iso: Waiting for VMware to clean up after itself...
    ==> vmware-iso: Deleting unnecessary VMware files...
        vmware-iso: Deleting: output-vmware-iso/startMenu.plist
        vmware-iso: Deleting: output-vmware-iso/vmware.log
        vmware-iso: Deleting: output-vmware-iso/ztps.plist
        vmware-iso: Deleting: output-vmware-iso/ztps.vmx.lck/M62713.lck
    ==> vmware-iso: Cleaning VMX prior to finishing up...
        vmware-iso: Detaching ISO from CD-ROM device...
    ==> vmware-iso: Compacting the disk image
    Build 'vmware-iso' finished.
    
    ==> Builds finished. The artifacts of successful builds are:
    --> vmware-iso: VM files in directory: output-vmware-iso
    ```


####References
http://www.packer.io/docs/builders/vmware-iso.html