How to install Ubuntu using serial console
==========================================

This instruction show you how to install Ubuntu with serial console without VGA.
We have tested the following instructions under UEFI boot and legacy boot mode.

USB boot-disk setup
------------------------------------------

1. Download an ubuntu server amd64 iso file
2. Create a USB boot disk using unetbootin
3. Modify the following files. If you still use legacy bios boot mode, modify `isolinux/isolinux.cfg`, `isolinux/txt.cfg`, `syslinux.cfg`. If you use UEFI boot mode, modify `boot/grub/grub.cfg` only.

	- `isolinux/isolinux.cfg`

	```
	# D-I config version 2.0
	include menu.cfg
	default menu.c32
	prompt 0
	timeout 0
	```
	- `isolinux/txt.cfg`

	```
	default install
	label install
	  menu label ^Install Ubuntu Server
	  kernel /install/vmlinuz
      append vga=normal initrd=/install/initrd.gz -- console=tty0 console=ttyS0,115200n8 nosplash debug -
	```

	- `syslinux.cfg`

	```
	CONSOLE 0
	SERIAL 0 115200 0

	default menu.c32
	prompt 0
	menu title UNetbootin
	timeout 100

	label unetbootindefault
	kernel /ubnkern
    append vga=normal initrd=/ubninit nomodeset askmethod console=tty0 console=ttyS0,115200n8
	```
    
    - `boot/grub/grub.cfg`
    
    Add serial console options and remove quiet option in the menuentry of "Install Ubuntu Server"
    
	```    
    menuentry "Install Ubuntu Server" {
        set gfxpayload=keep
        linux   /install/vmlinuz  file=/cdrom/preseed/ubuntu-server.seed vga=normal console=tty0 console=ttyS0,115200n8 ---
        initrd  /install/initrd.gz
        }
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

3. Select boot partition

	- Enter "Boot" Tab
    
    Set `UEFI USB disk boot` or `USB disk` as first boot priority at Boot Option

4. Save current BIOS configuration and reboot

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
An Ubuntu 14.04 installation boot disk may be "/dev/sda" in case that you use USB boot.

    ```
    /dev/sdb
    ```

3. Install SSH daemon and avahi-daemon to configure the system after this installation.
4. (Optional) When you see the dialog at "Finish the installation", check "<Go Back>" and select "start shell" to check host IP address in a DHCP environment with the following command.

    ```
    $ ip addr show
    ```

First boot after the installation
-----------------------------------------
1. Enter `editing mode` of grub boot option with key `e`, when you see grub boot menu on serial console.
2. Add console options after `ro` in linux boot options

	```
    linux ..... ro console=tty0 console=ttyS0,115200n8
	```
3. Boot linux with current grub configuration by `Ctrl-x`

Post setup
------------------------------------------
1. Grub configuration

	- Edit /etc/default/grub as follows:

	```
	GRUB_CMDLINE_LINUX_DEFAULT=""
	GRUB_TERMINAL='serial console'
	GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8"
	GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
	```

	- Update grub configuration

	```
	# update-grub
	```

Tested Ubuntu and Hardware
------------------------------------------
## Ubuntu version
- Ubuntu 14.04 LTS
- Ubuntu 15.10
- Ubuntu 16.04 LTS
- Ubuntu 16.04.1 LTS

## Tested hardware
- Lanner FW-7551
- Lanner FW-8896
- Lanner NCA-4010
- Riava Rangeley server


Reference
------------------------------------------
- http://pcengines.info/forums/?page=post&id=E25612E9-84F0-4DCF-A876-1E92FD1D065C
- https://help.ubuntu.com/community/SerialConsoleHowto
