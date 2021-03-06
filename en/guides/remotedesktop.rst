.. http://doc.slitaz.org/en:guides:remotedesktop
.. en/guides/remotedesktop.txt · Last modified: 2010/11/21 18:42 by seawolf

.. _remotedesktop:

Remote Desktop
==============

:author: seawolf


NoMachine (NX)
--------------

.. important::
   This section is under construction and has not been verified by others.
   It has been created from one particular (working) configuration.

`NoMachine <http://www.nomachine.com>`_ (NX) is a propietary remote desktop system, supporting multiple hosts and keypair-based log-ins.

.. note::
   This guide assumes you have a working SSH configuration for remote access.
   NoMachine/NX channels authentication and encrypted communications through SSH.
   This can be via password authentication or an RSA key pair; NoMachine/NX uses a DSA key pair to authenticate.

.. note::
   My (`seawolf <http://forum.slitaz.org/index.php/profile/20/seawolf>`_) personal SSH configuration uses a non-standard port and key pair-based, password-less authentication.
   The differences in this guide to a standard, password-protected SSH configuration using port 22 are irrelevant as the default is given initially.

There are three parts to a NX system:

#. the **server** is the machine to which you connect;
#. the **node** is one in a group of resources that hosts your session — this can be the server;
#. the **client** that connects to a server (and in turn, the node).


Download the Software
---------------------

There are three packages available to `download <http://www.nomachine.com/download-package.php?Prod_Id=2071>`_ from NoMachine.
All clients must have installed the client package, whereas all three packages must be installed on servers and nodes.
This is because parts of the client package is used by the node, parts of which are used by the server.

* Decompress the three NX packages (client, node, server) on the server into :file:`/usr` to create :file:`/usr/NX` directory.
* Decompress the client package on the client(s).


Server Configuration
--------------------

The automated commands are not compatible with BusyBox and the SliTaz configuration, so the installation commands need to be edited:

* Create the symlinks :file:`/etc/rc.d/rc#.d/` all pointing to :file:`/etc/init.d/`.
  This can be easily achieved with the following command performed as the *root* user:

  .. code-block:: console

     # for NUM in 0 1 2 3 4 5 6 ; do cp /etc/rc.d/init.d /etc/rc.d/rc$NUM.d; done

* Modify :file:`/usr/NX/scripts/setup/nxserver` by:

  * fixing the user add/del commands in lines 924, 963, 984.
  * comment out the command in line 956 by placing a hash immediately after the opening quotation mark (command not needed)

* Install the server:

  .. code-block:: console

     $ sudo /usr/NX/scripts/setup/nxserver --install fedora

  * the OS doesn't really matter but it's the closest match for the numerous :file:`/etc/init.d` commands

* Change the server name & SSH port (if necessary) in :file:`/usr/NX/etc/server.cfg` (line 31, 36 & 236)
* Allow administrative logins in line 87 of :file:`/usr/NX/etc/server.cfg`


Node Configuration
------------------

The automated commands are not compatible with BusyBox and the SliTaz configuration, so the installation commands need to be edited:

* Modify :file:`/usr/NX/scripts/setup/nxnode` by:

  * specify SSH port in line 43 (if necessary)
  * change to :file:`local.sh` on line 1305

* Install the node:

  .. code-block:: console

     $ sudo /usr/NX/scripts/setup/nxnode --install fedora

  * ignoring warning about CUPS detection if you don't have a printer

* Change server name & SSH port (if necessary) in :file:`/usr/NX/etc/node.cfg` (line 32, 342)


User Configuration
------------------

* Enable the nx user account by using:

  .. code-block:: console

     $ sudo passwd -u nx

* Confirm this with:

  .. code-block:: console

     $ sudo /usr/NX/bin/nxserver --usercheck <username>

     NX> 900 Verifying public key authentication for NX user: <username>.
     NX> 900 Adding public key for user: <username> to the authorized keys file.
     NX> 716 Public key added to: /home/ <username> /.ssh/authorized_keys2.
     NX> 900 Verifying public key authentication for NX user: <username>.
     NX> 900 Public key authentication succeeded.
     NX> 999 Bye.

You should now be able to log-in to the server/node using a normal username and password.


Recreating Keys
---------------

When the default key pair authenticates correctly, they should be regenerated for security reasons.
Issue the following command to create a new key pair:

.. code-block:: console

   $ sudo /usr/NX/bin/nxserver --keygen

New keys should be created.
Distribute the secret key :file:`/usr/NX/share/keys/default.id_dsa.key` to clients and import it in the client GUI (:menuselection:`Configure --> General --> Server --> Key… --> Import`).

Restart the server (:command:`sudo /usr/NX/bin/nxserver --restart`) to complete the changes.


Tips
----

* If you want to use key pair and password-less authentication, ammend :file:`/etc/ssh/sshd_config` with::

    PasswordAuthentication no
    AllowUsers nx //other usernames//

* Open ports 5000-5200 for an unencrypted connection.
  This is because after a successful authentication has taken place the client reconnects to a display in the range starting at 'DisplayBase' up to the value ('DisplayBase' + 'DisplayLimit').
  These parameters default to the values "1,000" and "200" respectively and TCP port numbers are obtained by adding the value "4,000" to the display numbers, thus giving 5000 and 5200.
  If encrypted display is enabled, all traffic is piped through SSH.

* If the client fails to connect to the server with the following messages::

    NX> 203 NXSSH running with pid: <PID>
    NX> 285 Enabling check on switch command
    NX> 285 Enabling skip of SSH config files
    NX> 285 Setting the preferred NX options
    NX> 200 Connected to address: <IP address> on port: <SSH port>
    NX> 202 Authenticating user: nx
    NX> 208 Using auth method: publickey
    NX> 204 Authentication failed.

then the :file:`/usr/nx/home/nx/.ssh/authorized_keys2` file is likely at fault.
