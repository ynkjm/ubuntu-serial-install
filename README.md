How to install Ubuntu using serial console
==========================================

USB boot-disk setup
------------------------------------------

1. Download ubuntu 14.04 server amd64 iso file
2. Create USB boot disk using unetbootin
3. Modify the following files

	- isolinux/isolinux.cfg

	```
	# D-I config version 2.0
	CONSOLE 0
	SERIAL 0 115200 0
	include menu.cfg
	default vesamenu.c32
	prompt 0
	timeout 0
	```
	- isolinux/txt.cfg

	```
	default install
	label install
	  menu label ^Install Ubuntu Server
	  kernel /install/vmlinuz
	  append  file=/cdrom/preseed/ubuntu-server.seed vga=788 initrd=/install/initrd.gz -- console=ttyS0,115200n8 quiet –
	```
	
	- syslinux.cfg
	
	```
	CONSOLE 0
	SERIAL 0 115200 0
	
	default menu.c32
	prompt 0
	menu title UNetbootin
	timeout 100
	
	label unetbootindefault
	kernel /install/netboot/ubuntu-installer/amd64/linux
	append initrd=/install/netboot/ubuntu-installer/amd64/initrd.gz tasks=standard pkgsel/language-pack-patterns= pkgsel/install-language-support=false vga=788 -- console=ttyS0,115200n8 -- quiet
	```

Hardware configuration
------------------------------------------
1. Enter BIOS.
2. Setup serial port setting to allow console redirection.

	- Enter "Advanced Setup Configurations" Tab

	    - Enter "Super IO Configuration" option

			```
			Serial Port 0 Configuration /Serial Port 1 Configuration
			Serial Port: Enabled
			```
			
		- Enter "Serial Port Console Redirection" option

			```
			Terminal Type: VT100+
			Bits Per second: 115200
			Data Bits: 8 Bits
			Parity: None
			Stop Bits: 1
			Flow Control: None
			VT-UTF8 Combo Key Support: Enabled
			Recorder Mode: Disabled
			Resolution 100x31: Enabled
			Legacy OS Redirection Resolution: 80x25
			Putty KeyPad: VT100
			Redirection After BIOS Post: Always Enable
			```
			
3. Save current BIOS configuration and reboot

Client setup
------------------------------------------
1. Attach serial console from client

```
	% screen /dev/ttyS0 115200
```

Ubuntu Installation
------------------------------------------
1. Proceed normal Ubuntu installation.
2. At the phase of GRUB installation, select your correct boot disk for boot loader.
An Ubuntu installation boot disk may be "/dev/sda" in case that you use USB boot.

```
	/dev/sdb
```

Post setup 
------------------------------------------
1. Grub configuration

	- Edit /etc/default/grub as follows:

			```
			GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8"
			GRUB_TERMINAL=serial
			GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
			```
	
	- Update grub configuration

			```
			# updaet-grub
			```

Reference
------------------------------------------
- http://pcengines.info/forums/?page=post&id=E25612E9-84F0-4DCF-A876-1E92FD1D065C
- https://help.ubuntu.com/community/SerialConsoleHowto
