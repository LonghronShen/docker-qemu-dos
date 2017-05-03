# qemu for Docker Server

This image can be used standalone, or as a base for other images.

It provides a qemu environment and a VNC console for it, running on
port 5901.  This can be used for various guest OSs, but the image
has special additional support for running DOS guests.

# Install and run

You can install with:

    docker pull jgoerzen/qemu

And run with:

    docker run -d -p 5901:5901 --name myqemu jgoerzen/qemu

# Installed files for DOS

Under `/qemu`, you will find `dosbox.conf` (the config file), to which you
can easily append autoexec statements.  Also, there are subdirectories
for drives C: through H: and Y:.  This image will install FreeDOS commands
into `Y:\DOS` (pulled from dosemu, as it happens) and make sure that the DOSBox
and FreeDOS commands are both on the system's PATH.

`Y:\SCRIPTS` is also created and put on the PATH, though empty by default;
it is a place where you can add scripts and such that run later.

Child images may, but are not required to, follow this convention:

 - Drive C for the main application (probably a volume, since many write to their CWD)
 - Drive D for helper applications (perhaps also a volume, since many write to their CWD)
 - Drive Y for static utilities/scripts, that would not need to be a volume.

This image uses supervisor; please see the supervisor/ directory for
examples.  Adding your own processes is very simple.

# Environment variables

 - `VNCPASSWORD` can set the password for the VNC console
   (maximum 8 characters, a limitation of tightvncserver).  If you do not set
   one, a random password will be assigned on each start of the container, and
   logged in the docker logs.

# VNC-based console

VNC is exposed on port 5901.  You can connect to this port.  You will see, by default,
an xterm (white) and a DOSBox terminal (black) running here, though
child images may alter these defaults.  If you do not see a DOSBox terminal,
then the command `dosboxconsole` should get one for you.

# Sources

This is prepared by John Goerzen <jgoerzen@complete.org> and the source
can be found at https://github.com/jgoerzen/docker-bbs/tree/master/qemu-dos

# Included Software

- Parted version 2 is pulled in from an old Debian version.  This version is
  used because it can resize partitions (resize command dropped in parted 3.x)
- FIXME: more stuff

The DOS drive C image is prepared from FreeDOS with standard "full install" plus
the network basics as documented in [their HOWTO](http://wiki.freedos.org/wiki/index.php/Networking_FreeDOS_-_Quick_Networking_HowTo).  To this, only XFS plus the CRT patch
have been added.  



The data sharing comes from XFS inside DOS, and UNFS3 on Linux.  

# References and information

- [User-space NFS server UNFS3](http://unfs3.sourceforge.net/)
- [XFS, a DOS NFS client](ftp://ftp.cc.umanitoba.ca/software/pc_network/)
  - [Info on PATCHCRT to address Runtime error 200](http://www.pcmicro.com/elebbs/faq/rte200.html)
- [Using FreeDOS on QEMU](https://en.wikibooks.org/wiki/QEMU/FreeDOS)
- [FreeDOS](http://www.freedos.org/)
- FreeDOS client
  - [FreeDOS with client](https://www.lazybrowndog.net/freedos/virtualbox/?page_id=8)
  - [Networking FreeDOS](http://wiki.freedos.org/wiki/index.php/Networking_FreeDOS_-_MS_Client)
  - [Share a Linux folder with FreeDOS](https://www.lazybrowndog.net/freedos/virtualbox/?page_id=374)
- [QEMU networking](https://en.wikibooks.org/wiki/QEMU/Networking#SMB_server)
  - [QEMU's SMB server](https://wiki.archlinux.org/index.php/QEMU#QEMU.27s_built-in_SMB_server)
- [strace on Docker](https://github.com/moby/moby/issues/21051)
  - magic incantation was: --privileged --cap-add SYS_PTRACE --security-opt seccomp:unconfined
- Dealing with DOS not idling CPU
  - [VirtualBox Heat](http://wiki.freedos.org/wiki/index.php/VirtualBox_-_Heat)
  - [DOSIDLE commands](http://www.scampers.org/steve/vmware/)
- [Dealing with RunTime Error 200](http://www.pcmicro.com/elebbs/faq/rte200.html)

