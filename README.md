# ARM64 VM on macOS with libvirt + QEMU
## Installing libvirt and QEMU

1. First, install homebrew, which is a package manager for macOS.
2. Run brew install qemu gcc libvirt.
3. Since macOS doesn't support QEMU security features, we need to disable them:
    echo 'security_driver = "none"' >> /opt/homebrew/etc/libvirt/qemu.conf
    echo "dynamic_ownership = 0" >> /opt/homebrew/etc/libvirt/qemu.conf
    echo "remember_owner = 0" >> /opt/homebrew/etc/libvirt/qemu.conf
4. Finally start the libvirt service, with brew services start libvirt. It will start after boot as well.

## Installing Ubuntu Server 20.04 for ARM

There are two ways to access the virtual display of the VM, either using a VNC client or the virt-viewer program. I recommend RealVNC Viewer. The VNC client is responsive and quick to install, but if you have multiple VMs you need to manually manage the different ports. The rest of this guide uses VNC.

1. Create a vms folder in your home directory, and generate a disk image. Change 50g to the size of your prefered disk:

    mkdir ~/vms && cd ~/vms
    qemu-img create -f qcow2 ubuntu.qcow2 50g
    
2. Download the ARM Ubuntu Server 20.04 Install Image and my ARM libvirt XML template and place the .iso and .xml files in the same folder. (macOS <= 12.3 needs an older XML template).

3. Modify the following elements in the ubuntu.xml file to match your your VM preferences and file paths. Save, then run virsh define ubuntu.xml followed by virsh start ubuntu.

    <domain type='qemu' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>
    <name>name of the VM</name>
    <memory unit='GB'>how much ram</memory>
    <vcpu>how many cpus</vcpu>
    ...
    <disk type='file' device='disk'>
    <source file='full path to your qcow2 file'/>
    ...
    <disk type='file' device='disk'>
    <source file='full path to your install iso'/>

4. virsh console ubuntu


5. Install Ubuntu Server normally, making sure to enable the SSH server. Once it restarts you can connect to the VM from your terminal by running ssh -p 2222 user@localhost.

6. To send a shutdown signal to your VM, run virsh shutdown ubuntu. To force shutdown, run virsh destroy ubuntu.

7. To forward a port, e.g. port 443 from the VM to port 8443 locally, run the following: ssh -p 2222 -L8443:localhost:443 user@localhost

## Multiple VMs

If you want to create multiple VMs, create an XML file for each machine with a unique UUID, VM name, and VNC port. Also, change the hostfwd argument so that each VM exposes a different port for SSH, e.g. 2223 instead of 2222. After you have defined them all, you can get a list of the VMs that are currently running with virsh list.# ARM64 VM on macOS with libvirt + QEMU
## Installing libvirt and QEMU

1. First, install homebrew, which is a package manager for macOS.
2. Run brew install qemu gcc libvirt.
3. Since macOS doesn't support QEMU security features, we need to disable them:
    echo 'security_driver = "none"' >> /opt/homebrew/etc/libvirt/qemu.conf
    echo "dynamic_ownership = 0" >> /opt/homebrew/etc/libvirt/qemu.conf
    echo "remember_owner = 0" >> /opt/homebrew/etc/libvirt/qemu.conf
4. Finally start the libvirt service, with brew services start libvirt. It will start after boot as well.

## Installing Ubuntu Server 20.04 for ARM

There are two ways to access the virtual display of the VM, either using a VNC client or the virt-viewer program. I recommend RealVNC Viewer. The VNC client is responsive and quick to install, but if you have multiple VMs you need to manually manage the different ports. The rest of this guide uses VNC.

1. Create a vms folder in your home directory, and generate a disk image. Change 50g to the size of your prefered disk:

    mkdir ~/vms && cd ~/vms
    qemu-img create -f qcow2 ubuntu.qcow2 50g
    
2. Download the ARM Ubuntu Server 20.04 Install Image and my ARM libvirt XML template and place the .iso and .xml files in the same folder. (macOS <= 12.3 needs an older XML template).

3. Modify the following elements in the ubuntu.xml file to match your your VM preferences and file paths. Save, then run virsh define ubuntu.xml followed by virsh start ubuntu.

    <domain type='qemu' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>
    <name>name of the VM</name>
    <memory unit='GB'>how much ram</memory>
    <vcpu>how many cpus</vcpu>
    ...
    <disk type='file' device='disk'>
    <source file='full path to your qcow2 file'/>
    ...
    <disk type='file' device='disk'>
    <source file='full path to your install iso'/>

4. virsh console ubuntu


5. Install Ubuntu Server normally, making sure to enable the SSH server. Once it restarts you can connect to the VM from your terminal by running ssh -p 2222 user@localhost.

6. To send a shutdown signal to your VM, run virsh shutdown ubuntu. To force shutdown, run virsh destroy ubuntu.

7. To forward a port, e.g. port 443 from the VM to port 8443 locally, run the following: ssh -p 2222 -L8443:localhost:443 user@localhost

## Multiple VMs

If you want to create multiple VMs, create an XML file for each machine with a unique UUID, VM name, and VNC port. Also, change the hostfwd argument so that each VM exposes a different port for SSH, e.g. 2223 instead of 2222. After you have defined them all, you can get a list of the VMs that are currently running with virsh list.