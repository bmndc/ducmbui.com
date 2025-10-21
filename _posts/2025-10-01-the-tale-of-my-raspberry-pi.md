---
layout: post
category: raspberry-pi
last_modified_date: 2025-10-06
---
# the tale of my Raspberry Pi

(Part 1 of a five-plus-part story on my experience of getting a Raspberry Pi and setting it up to be my personal server for self-hosting services.)

I was on top of the world when I got my first Raspberry Pi about three years ago.

Probably because the Pis were (and still are) ever so costly to buy here with our current exchange rate, to the point that I had to save up my allowance to get one. Or perhaps there were no micro-electronic hardware stores on Tran Dai Nghia St. that were selling genuine Pis at the time, and mine had to be delivered from one of the approved distributors in Ho Chi Minh City. Or maybe I was getting a Pi at [the worst time of the global Pi supply shortage].

But most importantly, I was watching [Rob's YouTube videos] on using a Raspberry Pi as a companion computer for the iPad. Rob was able to power his Raspberry Pi 4 with his iPad Pro and a Thunderbolt USB-C cable, and open a <abbr title="Secure Shell">SSH</abbr> on the Pi over that USB-C connection. I dreamed of a day when I can carry my iPad and the Pi around and work anywhere I want.

<!-- TODO: something about the state of getting a Pi here? -->

It was until I realised that I should've got a personal laptop instead of having my goldfish memory remember to bring the USB-C cable.

Alas, since then I used the Pi for other things than I intended: [converting it into what's basically an Android set-top-box] so I can watch YouTube on my LG TV full of crappy bloatware, [installing KDE on Raspberry Pi OS] and use it as a computer, or [replacing Raspberry Pi OS with Arch Linux ARM]. But I just left it running, messing around whenever I want to be in a Linux environment.

More recently, I stumbled across [this Hacker News comment] about using [ArchiveTeam's Warrior tool] to preserve links on the Internet from being inaccessible (called link rot). My interest suddenly got piqued by supporting the team's effort and running a version of Warrior on a spare computer at home. The Pi came to my mind first, but I soon came aware that [the Warrior tool is preserved for `amd64` desktop computers] and I cannot run it on my Pi.

Nonetheless, I learned about Docker and the self-hosting community, the people who don't want to be bounded by the Terms of Services placed upon by some massive companies.

I had been hosting my website on GitHub Pages, before moving it to Sourcehut. However, having the website on a third-party service meant I'm limited to what that service allows me to do. [Sourcehut, for example], prohibits loading scripts and styles from <abbr title="Content Delivery Networks, a.k.a. centrally hosted files">CDNs</abbr> or having the content of the site over 1GB in size. _(well, I try not to use JavaScript on this website, nor is it that big now... anyway)_

And so begins my journey of moving my personal website to a self-hosted web server of my own, plus maybe a password manager and an internal VPN between my devices.

## Notes on [installing 64-bit Arch Linux ARM on Pi 4] and overclocking

I shouldn't say the process went smoothly, otherwise this wouldn't be a multi-part story :/

Once I followed the instructions to install the `aarch64` version of Arch Linux ARM, my Pi 4 kept running into kernel panics as it went through the boot process with the generic U-Boot loader. I kept around a spare USB stick with a Raspberry Pi OS installation on it so I can investigate the filesystem.

```
mmc1: error -5 whilst initialising SD card
```

After doing some research, [as a member pointed out on the Arch Linux ARM forum], it turned out the `boot.txt` in the boot partition of the new system was using the memory address variable `fdt_addr_r` instead of `fdt_addr`. `fdt_addr_r` doesn't seem to exist on newer revisions of the Pi 4 than 1.4.

I was able to correct the variables in the `/boot/boot.txt` file, install `u-boot-tools` in Raspberry Pi OS and run the `./mkscr` executable:

```diff
@@ -1,16 +1,16 @@
  # After modifying, run ./mkscr

  # Set root partition to the second partition of boot device
  part uuid ${devtype} ${devnum}:2 uuid

  setenv bootargs console=ttyS1,115200 console=tty0 root=PARTUUID=${uuid} rw rootwait smsc95xx.macaddr="${usbethaddr}"

  if load ${devtype} ${devnum}:${bootpart} ${kernel_addr_r} /Image; then
  	if load ${devtype} ${devnum}:${bootpart} ${fdt_addr_r} /dtbs/${fdtfile}; then
   	  if load ${devtype} ${devnum}:${bootpart} ${ramdisk_addr_r} /initramfs-linux.img; then
-       booti ${kernel_addr_r} ${ramdisk_addr_r}:${filesize} ${fdt_addr_r};
+       booti ${kernel_addr_r} ${ramdisk_addr_r}:${filesize} ${fdt_addr};
      else
-       booti ${kernel_addr_r} - ${fdt_addr_r};
+       booti ${kernel_addr_r} - ${fdt_addr};
      fi;
    fi;
  fi
```

```sh
cd /dev/mmcblk0p1/boot/
sudo chmod +x ./mkscr && sudo ./mkscr
# OR
cd /dev/mmcblk0p1/boot/
sudo mkimage -A arm -T script -O linux -d boot.txt boot.scr
```

In addition to that, I was able to follow [the overclocking instructions on Raspberry Pi Official Magazine] and boost the CPU clock of my Pi 4 to 2.1GHz. Overclocking probably wouldn't hurt since the Pi 400 and the Compute Module were also overclocked, and that I have a fan (it didn't show any temperature warnings when I ran Raspberry Pi OS overclocked.)

Since the `/boot/config.txt` file of the U-Boot bootloader is the same as the one on the Raspberry Pi OS, basically I had to add the boot flags in `/boot/config.txt`:

```config
over_voltage=6
arm_freq=2100
gpu_freq=750
```

---

If you have any ideas on running ArchiveTeam's Warrior tool on a Raspberry Pi (I'd rather not go through emulation), or just want to correct my use of the word "but", feel free to send me an e-mail, discuss on GitHub or mention me on Mastodon!

[the worst time of the global Pi supply shortage]: https://www.raspberrypi.com/news/production-and-supply-chain-update/
[Rob's YouTube videos]: https://www.youtube.com/playlist?list=PLRiHAWwt3g5Nz9Vv0Nd6VbSsiPfQccOPA
[converting it into what's basically an Android set-top-box]: https://konstakang.com/devices/rpi4/
[installing KDE on Raspberry Pi OS]: https://linuxhint.com/install-kde-plasma-raspberry-pi/
[replacing Raspberry Pi OS with Arch Linux ARM]: https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-4
[this Hacker News comment]: https://news.ycombinator.com/item?id=44877724
[ArchiveTeam's Warrior tool]: https://wiki.archiveteam.org/index.php/ArchiveTeam_Warrior
[the Warrior tool is preserved for `amd64` desktop computers]: https://wiki.archiveteam.org/index.php/ArchiveTeam_Warrior#Can_I_run_the_Warrior_on_ARM_or_some_other_unusual_architecture?
[Sourcehut, for example]: https://srht.site/limitations/
[installing 64-bit Arch Linux ARM on Pi 4]: https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-4
[as a member pointed out on the Arch Linux ARM forum]: https://archlinuxarm.org/forum/viewtopic.php?f=67&t=15422&start=20#p67299
[the overclocking instructions on Raspberry Pi Official Magazine]: https://magazine.raspberrypi.com/articles/how-to-overclock-raspberry-pi-4
