# Helios4 Build

* ## Contents

  Project Scope  
  Post-Install  
  Project Scope  
  Post-Install  
  Not Finished
* ## Project Scope

  RAID56 performance goal is 75 Mbit/s (9.375 MB/s), eBox upload speed.
* ## Post-Install

  * pwm fan control  

    pwm control in `/etc/udev/rules.d/`​: [pwm @ kobol](https://wiki.kobol.io/helios4/pwm/)

    * other fan control method? using Linux thermal framework?

  * ​`hdparm`​ power management

    * apply on boot w/ `udev`​

      * https://wiki.archlinux.org/title/hdparm#Persistent_configuration_using_udev_rule
    * apply on wake w/ `systemd-udevd`​
  * RAID56 setup

    * ​`mdadm`​ configuration w/ `btrfs`​
    * ​`btrfs`​ metadata copy on OS drive
    * performance tuning : https://raid.wiki.kernel.org/index.php/Performance
  * power saving script

    ```bash
    #!/bin/bash

    echo "hello, $USER. The IDLE power saving is running now .."
    echo " "

    #turn off Power and PLL port0
    devmem 0xf10a0004 w 0x8962
    #turn off Power and PLL port1
    devmem 0xf10a0804 w 0x8901
    #turn off Power and PLL port2
    devmem 0xf10a1004 w 0x8901
    #turn off Power and PLL port5
    devmem 0xf10a2804 w 0x8962

    #turn off PHY Isolation Mode port0
    devmem 0xf10a0098 w 0x2922
    #turn off PHY Isolation Mode port1
    devmem 0xf10a0898 w 0x2922
    #turn off PHY Isolation Mode port2
    devmem 0xf10a1098 w 0x2922
    #turn off PHY Isolation Mode port5
    devmem 0xf10a2898 w 0x2922

    #turn on Power Management Clock Gating cntl
    devmem 0xf1018220 w 0x106101a
    #turn off PEX 
    devmem 0xf1018204 w 0x707c000
    #turn off SSCG PLL 
    devmem 0xf10184b0 w 0xd05832a
    #turn off PHY port0 
    devmem 0xf1018300 w 0x4406001
    #turn off PHY port1
    devmem 0xf1018328 w 0x8806001
    #turn off PHY port2
    devmem 0xf1018350 w 0x8806001
    #turn off PHY port5
    devmem 0xf10183c8 w 0x440E004

    echo " "
    echo " You can check your power measurement now."
    echo " "
    ```
  * dynamic frequency scaling (may already be implemented)

    ```bash
    Steps:
    1. Compile Linux Kernel with CONFIG_CPUFREQ and CONFIG_CPUFREQ_DT
    	Enable CPU Power Management -> CPU Frequency scaling
    	CPU Power Management -> CPU Frequency scaling -> Default CPUFreq governor to Userspace
    	CPU Power Management -> CPU Frequency scaling  -> Generic DT based cpufreq driver
    2. Remove the second CPU in DTS file
    3. Make sure using the latest 3.10 LSP release (2015_T1.1p7)
     
    Under Linux:
            # Check CPU information
                    cat /proc/cpuinfo
            #  show current CPU frequency
                    Cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_cur_freq
            # show available CPU frequencies
                    Cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_frequencies
           # Change CPU frequency
                  echo new_speed > /sys/devices/system/cpu/cpu0/cpufreq/scaling_setspeed
    ```
* ## SLTS Kernel

  * [Kernel 6.1 @ Civil Infrastructure Project](https://git.kernel.org/pub/scm/linux/kernel/git/cip/linux-cip.git/?h=linux-6.1.y-cip-rebase)​

    * rebase kernel merges mainline patches w/ internal  development
    * [Helios4 patches @ alexgeorge repo clone](https://github.com/algeorge/helios4-armbian/tree/main/patch/kernel/archive/mvebu-6.1)

‍

# Helios4 Additional

\~ rclone for gdrive and onedrive : also has sftp  
\~ optional -- jupyter or onenote  
\~ optional --  
Helios4 -- practice on Azure or Google -- systemd nspawm for containerization https://wiki.archlinux.org/title/systemd-nspawn -- document the following future upgrades: crony replacement with systemd, coreutils replaced by busybox, glibc replaced by mussl, update main notes
