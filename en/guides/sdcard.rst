.. http://doc.slitaz.org/en:guides:sdcard
.. en/guides/sdcard.txt · Last modified: 2010/08/23 00:14 by linea

.. _sdcard:

Installing SliTaz to a MMC/SD-card
==================================

:author: monz, linea, jozee

SliTaz can be installed to a MMC/SD flash-memory card (the kind used in digital cameras) in either of two ways: in a LiveUSB version as used on a "memory-stick" (also called "thumb-drive", "flash-drive", or a variety of other names), or as a regular install as used on a hard-drive.
There are advantages and disadvantages to both methods.


LiveUSB-type installation
-------------------------

A memory-stick uses NAND flash memory which eventually wears out after about 100,000 read/write cycles.
SliTaz tries to minimize this wear, and thus prolong the life of the memory-stick, by storing the entire main root-file-system ("rootfs") as a compressed image in one file (:file:`rootfs.gz`), then uncompressing it into the machine's memory (RAM) when booting.
Only the :file:`/home` directory is normally written onto the memory-stick, unless the user issues this command:

.. code-block:: console

   # tazusb writefs [compression]

Where "[compression]" can be "lzma", "gzip", or "none".
This command re-writes the :file:`rootfs.gz` file onto the memory-stick, so that changes made to the system can be saved and used again at the next boot-up.
The big advantage to using this method is obviously that it prolongs the life of the USB device.
The big disadvantage is that booting takes a long time because of the use of compression, and the more packages you install to your rootfs system the longer it takes to boot.
The rootfs can also be saved without compression (i.e.,

.. code-block:: console

   # tazusb writefs none

), which will allow it to boot much faster.
This was not such a good option when SliTaz 1.0 was released in 2008, because memory-sticks had much less storage space then, but with 4 GB memory-sticks common now, it's not as much of a problem.
This same method can be used to install SliTaz to a MMC/SD card.


Regular "hard-drive"-type installation
--------------------------------------

.. compound::
   The other option is to treat the MMC/SD card as if it were a real hard-drive, and simply use:

   .. code-block:: console

      # gparted

   to format the card and set up partitions like on a regular hard-drive, and

   .. code-block:: console

      # slitaz-installer

   to do the actual installation.
   Be sure to also allow the installer to install GRUB onto the card, so that you can boot from it the normal way, same as you would from a regular hard-drive.


Example as used on my own Asus Eee-PC
-------------------------------------

The SliTaz installation which I put onto a MMC/SD-card (16 GB) was for my Asus Eee-PC 701, leaving the Eee's original Xandrox Linux installation intact on its SSD (solid-state drive), and keeping the MMC/SD-card with SliTaz always in the card-reader slot.
The Eee's BIOS needed to be changed to allow the SD-card to be the first boot device, so that the machine would boot into GRUB before anything else.
The :file:`/boot/grub/menu.lst` is setup to allow me to boot from either Xandros or SliTaz on the GRUB menu:

.. code-block:: shell

   # /boot/grub/menu.lst: GRUB boot loader configuration.
   #
   
   # By default, boot the first entry.
   default 0
   
   # Boot automatically after 8 secs.
   timeout 8
   
   # Change the colors.
   color yellow/brown light-green/black
   
   # To boot newest slitaz from : /dev/sdb5
   #
   title  SliTaz GNU/Linux (cooking - kernel 2.6.30.6)
     root (hd0,4)
   #  kernel /boot/vmlinuz-2.6.30.6-slitaz rootdelay=10 root=/dev/sdb5
   # the kernel line used to be necessary
   # but apparently a later upgrade of SliTaz commented it out
   # GRUB boots into SliTaz with no problem using only the root (hd0,4) command
   
   # To boot Asus eee pc Xandros
   #
   title Xandros (kernel vmlinuz-2.6.21.4-eeepc)
     root (hd1,0)
     kernel /boot/vmlinuz-2.6.21.4-eeepc quiet rw vga=785 irqpoll i8042.noloop=1 root=/dev/sda1
     initrd /boot/initramfs-eeepc.img

The first 3 partitions on my MMC/SD-card were originally used as storage for various parts of the Eee's Xandros system, with the 4th partition formatted as an Extended Partition and further divided into two, with Partition 5 used for the SliTaz install and Partition 6 for Linux-Swap.
But because this SliTaz installation has become my main one for the Eee, as I added more packages I finally decided to move parts of the SliTaz filesystem to other partitions and use this layout:

.. code-block:: console

   $ df -h
   Filesystem                Size      Used Available Use% Mounted on
   rootfs                    1.4G    413.3M    913.1M  31% /
   /dev/root                 1.4G    413.3M    913.1M  31% /
   tmpfs                   500.7M         0    500.7M   0% /dev/shm
   /dev/sdb2                 3.0G      1.4G      1.6G  46% /usr
   /dev/sdb3                 2.5G      2.1G    323.0M  87% /home
   /dev/sdb1                 7.4G      5.2G      1.9G  73% /home/shared

The :file:`/home/shared` directory is one which has documents shared by the Xandros system.

The Eee-PC was already set-up to recognize and read the MMC/SD-card.
But one day I accidentally moved the whole contents of :file:`/boot` to another directory and thus was unable to boot SliTaz.
When I inserted the card into a Toshiba Satellite A215-S5850 laptop (my biggest, most modern, and main laptop), I was surprised to find that the machine did not see the MMC/SD-card.
It was necessary to add a kernel package:

.. code-block:: console

   # tazpkg get-install linux-mmc

and then to load this module:

.. code-block:: console

   # modprobe mmc_block

After that, SliTaz was able to read the contents of the MMC/SD-card, and I was able to move the :file:`/boot` files back to where they belonged and fix the system.
If the output of

.. code-block:: console

   # lsmod

does not show ``mmc_block``, then that module must be loaded manually.
To have SliTaz always load it at boot-up, simply add it to the ``LOAD_MODULES`` line of :file:`/etc/rcS.conf`.
