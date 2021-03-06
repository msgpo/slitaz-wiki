.. http://doc.slitaz.org/en:scratchbook:gtk-apps
.. en/scratchbook/gtk-apps.txt · Last modified: 2011/04/23 23:21 by domcox

.. _scratchbook gtk apps:

GTK+ Applications
=================

:author: domcox

Compiliation and installation of applications using GTK+.


About
-----

This chapter describes the commands for the compilation and installation of GTK+ applications distributed by default on the SliTaz LiveCD.
The installation of GTK+ libraries are described in the :ref:`scratchbook gtk libs` chapter.


.. rubric:: Environmental variable (``$fs``)

If you do not specify any path to the rootfs directory, export the environmental variable:

.. code-block:: console

   # export fs=$PWD/rootfs

To check:

.. code-block:: console

   # echo $fs


leafpad-0.8.10 — Simple text editor
-----------------------------------

Website: http://tarot.freeshell.org/leafpad/

.. code-block:: console

   # wget http://savannah.nongnu.org/download/leafpad/leafpad-0.8.10.tar.gz
   # tar xzf leafpad-0.8.10.tar.gz
   # cd leafpad-0.8.10
   # ./configure --prefix=/usr
   # make
   # make DESTDIR=$PWD/_pkg install
   # strip _pkg/usr/bin/*


.. rubric:: Install in rootfs

.. code-block:: console

   # cp _pkg/usr/bin/* $fs/usr/bin
   # cp _pkg/usr/share/pixmaps/leafpad.png $fs/usr/share/pixmaps
   # cp -a _pkg/usr/share/locale/fr $fs/usr/share/locale


gitmail-0.4 — Ghost In The Mail, mail client
--------------------------------------------

Ghost in the mail allows users to quickly and easily send mail via SMTP.

Website: http://gitmail.sourceforge.net/

.. code-block:: console

   # wget http://switch.dl.sourceforge.net/sourceforge/gitmail/gitmail-0.4.tar.gz
   # tar xzf gitmail-0.4.tar.gz
   # cd GhostInTheMail-0.4
   # ./configure --prefix=/usr
   # make
   # make DESTDIR=$PWD/_pkg \
     gitmaildocdir=/usr/share/doc/GhostInTheMail \
     install
   # strip _pkg/usr/bin/*


.. rubric:: Install in rootfs

.. code-block:: console

   # cp _pkg/usr/bin/* $fs/usr/bin


gqview-2.0.4 — Images Manager
-----------------------------

Website: http://gqview.sourceforge.net/

.. code-block:: console

   # wget http://belnet.dl.sourceforge.net/sourceforge/gqview/gqview-2.0.4.tar.gz
   # tar xzf gqview-2.0.4.tar.gz
   # cd gqview-2.0.4
   # ./configure --prefix=/usr --mandir=/usr/share/man
   # make
   # make DESTDIR=$PWD/_pkg install
   # strip _pkg/usr/bin/*


.. rubric:: Install in rootfs

.. code-block:: console

   # cp _pkg/usr/bin/* $fs/usr/bin
   # cp _pkg/usr/share/pixmaps/* $fs/usr/share/pixmaps
   # cp -a _pkg/usr/share/locale/fr $fs/usr/share/locale


mtpaint-3.11 — Image creation and processing
--------------------------------------------

Website: http://mtpaint.sourceforge.net/

.. code-block:: console

   # wget http://switch.dl.sourceforge.net/sourceforge/mtpaint/mtpaint-3.11.tar.bz2
   # tar xjf mtpaint-3.11.tar.bz2
   # cd mtpaint-3.11
   # ./configure --cpu=i486 --prefix=/usr intl
   # make
   # strip src/mtpaint


.. rubric:: Install in rootfs

.. code-block:: console

   # cp src/mtpaint $fs/usr/bin
   # cp po/fr.mo $fs/usr/share/locale/fr/LC_MESSAGES/mtpaint.mo
   # cp src/icons1/icon.xpm $fs/usr/share/pixmaps/mtpaint.xpm


Transmission-0.72 — Lightweight BitTorrent client
-------------------------------------------------

Tranmission BitTorrent client is fast, lightweight and easy to use.
The compiled package provides the command line client (transmissioncli) and a GTK+ client (transmission-gtk).
We install the GTK+ client, the command line client is distributed as a separate SliTaz package (:file:`*.tazpkg`).

Website: http://transmission.m0k.org/

.. code-block:: console

   # wget http://download.m0k.org/transmission/files/Transmission-0.72.tar.gz
   # tar xzf Transmission-0.72.tar.gz

The archived version 0.72 is wrong:

.. code-block:: console

   # mv "Transmission .72" Transmission-0.72

   # cd Transmission-0.72
   # ./configure --prefix=/usr  --disable-openssl
   # make
   # strip gtk/transmission-gtk
   # strip cli/transmissioncli


.. rubric:: Install in rootfs

.. code-block:: console

   # cp gtk/transmission-gtk $fs/usr/bin
   # cp gtk/transmission.png $fs/usr/share/pixmaps
   # cp gtk/po/fr.mo $fs/usr/share/locale/fr/LC_MESSAGES/transmission-gtk.mo


emelfm2-0.3.5 — File Manager
----------------------------

The emelFM2 application is a file manager providing lots of useful functions, such as the mounting of devices, a text viewer, opening a terminal in the current directory and so on.

Website: http://emelfm2.net/

.. code-block:: console

   # cd ..
   # wget http://emelfm2.net/rel/emelfm2-0.3.5.tar.gz
   # tar xzf emelfm2-0.3.5.tar.gz
   # cd emelfm2-0.3.5
   # make PREFIX=/usr
   # make i18n PREFIX=/usr
   # make install PREFIX=$PWD/_pkg/usr
   # make install_i18n PREFIX=$PWD/_pkg/usr
   # strip -v _pkg/usr/bin/*
   # strip -v _pkg/usr/lib/emelfm2/plugins/*


.. rubric:: Install in rootfs

.. code-block:: console

   # cp _pkg/usr/bin/* $fs/usr/bin
   # cp -a _pkg/usr/lib/* $fs/usr/lib
   # cp -a _pkg/usr/share/pixmaps $fs/usr/share
   # cp -a _pkg/usr/share/locale/fr $fs/usr/share/locale


geany-0.11 — Integrated Development Environment
-----------------------------------------------

Geany is a simple, fast and light IDE offering colored syntax, tabs, autocompletion, aids to scripts and much more.

Website: http://geany.uvena.de/

To compile and run geany on SliTaz, you must have the libstdc++ and libgcc1 libraries, both provided by gcc (we recompiled with gcc-4.1.1), but you can copy the libraries from the host system.

Note: The force is with you, if you activate it via the option ``--enable-the-force``.

.. code-block:: console

   # wget http://mesh.dl.sourceforge.net/sourceforge/geany/geany-0.11.tar.gz
   # tar xzf geany-0.11.tar.gz
   # cd geany-0.11
   # ./configure --prefix=/usr --mandir=/usr/share/man \
     --disable-vte --enable-the-force
   # make
   # make DESTDIR=$PWD/_pkg install
   # strip -v _pkg/usr/bin/*


.. rubric:: Install in rootfs

.. code-block:: console

   # cp _pkg/usr/bin/* $fs/usr/bin
   # cp -a _pkg/usr/share/geany $fs/usr/share
   # cp _pkg/usr/share/pixmaps/geany.png $fs/usr/share/pixmaps
   # cp -a _pkg/usr/share/locale/fr $fs/usr/share/locale


gftp-2.0.18 — Fast and simple FTP client
----------------------------------------

The gFTP application is a fast and efficient FTP client with a GTK+ graphical interface.
Note that we compile without support for a text interface and SSL support.
Get, untar, configure, compile and install.

Website: http://www.gftp.org/

.. code-block:: console

   # wget http://www.gftp.org/gftp-2.0.18.tar.gz
   # tar xzf gftp-2.0.18.tar.gz
   # cd gftp-2.0.18
   # ./configure --prefix=/usr --mandir=/usr/share/man \
     --disable-ssl --disable-textport \
     --build=i486-pc-linux-gnu --host=i486-pc-linux-gnu
   # make
   # make DESTDIR=$PWD/_pkg install
   # strip _pkg/usr/bin/*


.. rubric:: Install in rootfs

SliTaz provides only the GTK+ client on the CD.
Note that "gftp" is just a small script that detects the environment (console or X) and launches the right interface:

.. code-block:: console

   # cp _pkg/usr/bin/gftp $fs/usr/bin
   # cp _pkg/usr/bin/gftp-gtk $fs/usr/bin
   # cp -a _pkg/usr/share/gftp $fs/usr/share
   # cp -a _pkg/usr/share/pixmaps $fs/usr/share
   # cp -a _pkg/usr/share/locale/fr $fs/usr/share/locale

To save a little space and avoid duplication, you can delete :file:`COPYING` (17 KB) included in :file:`/usr/share/gftp`.
The GNU licence is already present in :file:`/usr/share/licence`, if you want to create a symbolic link.


xpad-2.12 — Mini note taking application
----------------------------------------

The Xpad application can quickly take notes via various customizable (GTK+) windows.

Website: http://xpad.sourceforge.net/

.. code-block:: console

   # wget http://surfnet.dl.sourceforge.net/sourceforge/xpad/xpad-2.12.tar.bz2
   # tar xjf xpad-2.12.tar.bz2
   # cd xpad-2.12
   # ./configure --prefix=/usr --mandir=/usr/share/man \
     --build=i486-pc-linux-gnu --host=i486-pc-linux-gnu
   # make
   # make DESTDIR=$PWD/_pkg install
   # strip _pkg/usr/bin/*


.. rubric:: Install in rootfs

.. code-block:: console

   # cp _pkg/usr/bin/xpad $fs/usr/bin
   # cp -a _pkg/usr/share/pixmaps $fs/usr/share
   # cp -a _pkg/usr/share/locale/fr $fs/usr/share/locale
