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

Reference
------------------------------------------
- http://pcengines.info/forums/?page=post&id=E25612E9-84F0-4DCF-A876-1E92FD1D065C
- https://help.ubuntu.com/community/SerialConsoleHowto
