# Umbrel Setup

- [ ] **Before you start.**

  The instructions below include icons indicating the following warning levels:

  - :rotating_light: Money could be lost if warning is not heeded.

  - :warning: Problems will result if warning is not heeded.

  - :information_source: Important information to consider.

- [ ] **Make sure the hardware is ready.**

  The initial steps are based on [umbrel.com](https://umbrel.com/).

  You need all these (or equivalent) parts ready to assemble:
  - Raspberry Pi 4 (8GB recommended)
  - SSD (2TB recommended)

    :rotating_light: The SSD should NOT contain any data. If it was used previously, delete the partition(s) so that Umbrel will (re)initialize it. How to delete partitions is detailed in a later step.

  - Powered dock for the SSD
  - MicroSD card (128 GB recommended)
  - Raspberry Pi 4 power supply (the official one is recommended)
  - Ethernet cable to connect to LAN
  - Case/housing
  Additionally, the setup should be powered by a UPS in case of power failures.
  
  :warning: In case of lightning storms, perform a graceful shut down of the node and unplug the UPS to avoid damage until risk passes.

  :warning: It is advisable to have the MAC addresses of the device. Record it at the earliest opportunity.

- [ ] **Download Umbrel OS and Raspberry Pi Imager.**

  On the computer that you will use to flash the SD card, download the following files. This procedure assumes that a Windows PC will be used for this purpose, but any appropriate computer (with an SD card slot) should work similarly.

  [https://download.umbrel.com/umbrel-os.zip](https://download.umbrel.com/umbrel-os.zip) (931 MB)
  
  [https://downloads.raspberrypi.org/imager/imager_latest.exe](https://downloads.raspberrypi.org/imager/imager_latest.exe) (19 MB)
  
  Make sure to use the lastest versions.

- [ ] **Delete existing partition from SSD.**

  If the SSD was used with Umbrel previously, Umbrel will detect it and start up the old node, even if the SD card is flashed anew. This might be desirable for certain recovery cases, but assuming a full re-install is desired, old SSD partition should be deleted before proceeding. This will ensure that Umbrel does not resurrect the old partition but will instead reformat the drive to set it up anew. Deleting the old partition can be accomplished on any Linux system to which you can connect the drive, using the following commands:
  
  - Run `lsblk` to identify the SSD device.
  - Run `fdisk /dev/sdx` (where sdx is the SSD device identified above, which is usually /dev/sda).

    :rotating_light: Check the device carefully to avoid deleting the wrong partition from the wrong drive.

  - Enter `p` at the fdisk prompt to print a list of the drive's partitions.
  - Enter `d` to delete the (only) partition.
  - Enter `w` to write the change to disk.

    :information_source: You might receive an error indicating that the device is busy and that the changes will take effect only after reboot.

  Now the drive should be ready.

- [ ] **Flash Umbrel OS.**

  - Insert the SD card into a computer with an SD card slot (Windows PC assumed).

    :information_source: You might need a size adapter. The card should be detected automatically when plugged in. If prompts to format it and/or folder windows pop open, just cancel and/or close them.

  - Open Raspberry Pi Imager. (The installer is fast, you can run it every time you need the imager.)
  - Under CHOOSE OS, select Use custom, then browse to the Umbrel zip file downloaded earlier.
  - Under CHOOSE STORAGE, select the SD card.
  - Finally, click WRITE and confirm.

    :information_source: Again, some prompts and/or windows might pop open at the beginning and end of the writing process. Ignore, close, or cancel them.

  - When the writing and verification is finished, remove the SD card as prompted.
  - Close the imager.

- [ ] **Set up the hardware.**

  - Insert the microSD card into the Raspberry Pi 4 while the power is off.
  - Put the SSD in its dock, and plug the dock into any of the two USB 3.0 ports (blue colored) on the Raspberry Pi 4.

    :rotating_light: Unless the SSD was used by Umbrel before and was not erased in the earlier step, it will be automatically (re)formatted without any warning when you turn on the Raspberry Pi 4. Normally this is desired when setting up a new node, but if there was previously any data on the drive, make sure you have a copy of any seed words, passwords, or other critical information.

  - Connect one end of the ethernet cable to the Raspberry Pi 4 and the other end to any vacant port on your internet router, hub, or access point.

- [ ] **Power up the node.**

  - Connect the power supply to the SSD USB dock.
  - Connect the power supply to the Raspberry Pi 4 to turn it on.

    :information_source: It will take some time to initialize. Wait about 5 minutes before proceeding.

- [ ] **Find the IP address.**

  After 5 minutes, Umbrel OS should be accessible at [http://umbrel.local](http://umbrel.local) on any device that is connected to the same network as the Raspberry Pi 4. However, `.local` addresses do not always work, and you might have to find the DHCP-assigned IP address another way, such as by comparing the Pi's MAC address to the DHCP server's client list or to the output of the `arp -a` command. This, too, can be difficult, because the MAC address will only be in your computer's cache if you have recently communicated with the device. However, the following steps should work:

  - Open a shell window on any PC on the local network.
  - Ping all addresses on the local network that are in the range allocated for DHCP.
  - Run the command `arp -a`.
  - Look for the MAC address of the Raspberry PI 4.
  - Note the IP address that has been assigned to it.

- [ ] **Set up Umbrel.**

  Open the Umbrel user interface using either [http://umbrel.local](http://umbrel.local) or by typing in the discovered IP address into your browser's address bar.

  When the welcome screen appears, click `START` and fill in the necessary information:
  - For the name, enter any name. It is used only in greeting you.
  - For the password, use a secure tool to generate a new strong password.

    :rotating_light: Record this password in a secure place, because there is no other way to access the Umbrel node or reset the password if it becomes lost!

  - Click `NEXT`.

  At this point, you will be invited to install your first app. Just wait and keep the window open for now.

- [ ] **Set a permanent IP address.**

  :information: For the sake of this document, we will assume the desired fixed IP address should be 192.168.0.20 and the gateway is 192.168.0.2.

  Setting a permanent IP address should be done as follows:
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@umbrel.local` (use the IP if needed).

    :information_source: If this is the first time you are connecting, you must accept the secure fingerprint.

  - When prompted, enter the password selected previously.
  - Edit the DHCP configuration using the command `sudo nano /etc/dhcpcd.conf`.

    :information_source: You will be prompted for the password again.

  - Look for the following section near the bottom and uncomment/edit the appropriate lines as follows:

    ```
    # Example static IP configuration:
    interface eth0
    static ip_address=192.168.0.20/24
    #static ip6_address=xxxx:xxxx:xxxx:xxxx::ff/64
    static routers=192.168.0.2
    static domain_name_servers=8.8.4.4 8.8.8.8 xxxx:xxxx:xxxx:xxxx::1
    ```
  
  - You may substitute your favorite DNS server IP addresses.
  - Save the file (`Ctrl`+`X` to exit, then `Y` to confirm save, then `Enter` to accept the existing filename).

    :information_source: The IP address changes will not take effect until restart.

  - Close the terminal with the `exit` command.
  - Go back to the Umbrel window.
  - Restart the node by going to Settings (the gear icon on the toolbar) and selecting `RESTART`.
  - Close the browser tab.

- [ ] **Install Bitcoin Node.**

  Wait for about five minutes for Umbrel to restart.
  
  - Re-open the Umbrel console by pointing your web browser to [http://192.168.0.20](http://192.168.0.20) (or whatever IP address you configured, which is now in effect.)

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the same password as before.
  - Use the “Install your first app” prompt or go to the App Store icon in the bottom toolbar to search for Bitcoin Node and open its information page.
  - Click `INSTALL` to install the Bitcoin Node app.
  - Wait for the installation to complete. As indicated, it might take a while.

- [ ] **Configure the initial block download (IBD) for LAN only.**

  To save downloading the hundreds of gigabytes of blockchain data over the Internet, you can sync the new node by connecting to another peer on the same LAN that is already synced. To do this, do the following:

  - Open the Bitcoin Node app.
  - Wait for it to show some peer connections.
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@192.168.0.20`.
  - Run `sudo nano umbrel/app-data/bitcoin/data/bitcoin/bitcoin.conf` or similar to edit the bitcoind configuration file.

    :information_source: Enter the password when prompted.

  - Add the line `connect=192.168.0.10` (assuming this is the IP address of a Bitcoin Node on the LAN that is already synced, e.g. your production node).
  - Save the file (`Ctrl`+`X` to exit, then `Y` to confirm save, then `Enter` to accept the existing filename).

    :information_source: This configuration will get reset if `RESTORE DEFAULT SETTINGS` is clicked on the Bitcoin Node Advanced Settings page.

  - Back in the Umbrel Bitcoin Node window, go to the `···` Advanced Settings page of the Bitcoin Node app.
  - Turn off Tor for clearnet peers. For purposes of IBD, the following settings are recommended as of the time of writing:
    - Outgoing Connections to Clearnet Peers: On
    - Outgoing Connections to Tor Peers: On
    - **Connect to all Clearnet Peers over Tor: Off**
    - Outgoing Connections to I2P Peers: On
    - Incoming Connections: Off

      :warning: If incoming connections are allowed, Internet usage will be subject to the demand of the Bitcoin Network, which is unnecessary for the IBD.

    - Cache Size (MB): 450
    - Replace-By-Fee (RBF) for All Transactions: Off
    - Prune Old Blocks: Off
    - Network: mainnet

    :warning: Do not deviate from these or default settings without a clear understanding of the impact.

  - Restart Bitcoin Node by clicking `SAVE AND RESTART BITCOIN NODE`.

    :warning: The configuration items set in bitcoin.conf will get reset if `RESTORE DEFAULT SETTINGS` is clicked.

  Once the Bitcoin Node app has restarted, check to see that it shows only a single peer connection over Clearnet (without Tor).

  :warning: If it wont connect or repeatedly connects and disconnects, the other node might need to have a configuration line such as `whitelist=192.168.0.20` added to its bitcoin.conf and be restarted. This should be done in any case to avoid problems.

  :warning: Make sure the other node does not have tight constraints on the number of connections, as set by the `maxconnections` option in its bitcoin.conf. (The default of 10 should work.)

  Once connected, the IBD will happen over the LAN with that peer. You should see the synchronization progress in the app slowly over the course of a few days until the blockchain is fully synchronized.

- [ ] **Configure secure SSH connection to monitoring server.**

  This will allow scripts on the node to report data to a designated server securely.
  - SSH into the Raspberry Pi using a command such as `ssh umbrel@192.168.0.20`.
  - Create a new private/public key pair using the command `ssh-keygen`, accepting all defaults.
  - Copy the public key to the server using a command such as `ssh-copy-id monitoruser@192.168.0.15`, where 192.168.0.15 is the IP address of the monitoring server. When prompted, enter that server password.

  Using a command such as `ssh monitoruser@192.168.0.15`, you (or scripts) can now SSH from the Umbrel node to the monitoring server without a password. This will enable the next step.

- [ ] **Add monitoring script.**

  - Run the command `pico monitor.sh` to create the monitoring script. Paste in the code from [monitor.sh](monitor.sh).
  - Make sure the script is executable using a command such as `chmod a+x monitor.sh`.
  - Make sure the script is configured to run automatically by editing the root crontab, which can be done with the command `sudo crontab -e`.

    :information_source: When prompted for the Umbrel password, enter it.

    :information_source: You might be prompted to select an editor. The nano editor will work.

  - Add the following line to the file:

```
@reboot su -c /home/umbrel/monitor.sh umbrel 2>&1 > /dev/null
```

  - Exit using `Ctrl`+`X`. Confirm save with `Y` and `Enter` to accept the existing filename.
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.20](http://192.168.0.20).
  - Login using the usual password.
  - Open the Bitcoin Node app.
  - Click the `CONNECT` button.
  - In the window that opens, find the Bitcoin Core RPC Password and copy it.
  - Go back to the shell and edit the monitoring script using a command such as `pico monitor.sh`.
  - Look for the RPC_PW variable assignment near the top:
```
# Bitcoin Core RPC Password
RPC_PW="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```
  - Update the value in quotation marks by deleting it and pasting the value previously copied from Umbrel.

  For the monitoring script to run, the node needs to be re-started.

  - Restart the node by going to Settings and selecting `RESTART`.

  Alternatively, the script can be started manually without restarting Umbrel:
  
  - Find the running instance (if any) using a command such as `ps -aux|grep monitor.sh`.
  - Kill the running instance (if any) using a command such as `kill -9 <pid>` where <pid> is the process id of the running process.
  - Repeat the ps command to verify that the existing instance is no longer running.
  - Start the script anew. If you want to see any error or other output for debugging purposes, simply use `./monitor.sh`. If you wish to run it permanently even if the shell is closed or the connection is lost, use `nohup ./monitor.sh >/dev/null &`.

- [ ] **Install traffic meter.**

  Traffic monitoring is important where bandwidth limits and/or costs apply.
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@192.168.0.20`.
  - Install IP Flow Meter using a command such as `sudo apt install ipfm`.
  - Edit the configuration using a command such as `sudo pico /etc/ipfm.conf` and set the configuration to something desirable such as the following:
```
NOPROMISC
DEVICE eth0
LOG 192.168.0.20/255.255.255.255 NOT WITH 192.168.0.0/255.255.255.0
FILENAME "/var/log/ipfm/internet-daily-total"
DUMP EVERY 1 day
CLEAR ALWAYS
SORT TOTAL
RESOLVE
NEWLOG
LOG 192.168.0.20/255.255.255.255 WITH 192.168.0.0/255.255.255.0
FILENAME "/var/log/ipfm/local-daily-total"
DUMP EVERY 1 day
CLEAR ALWAYS
SORT TOTAL
RESOLVE
```
  :information_source: The configuration above will create two log files: one for traffic between this device and endpoints outside the local network (i.e. Internet traffic) and one for traffic between this device and other devices within the local network.

  Configure the traffic monitor to start automatically as follows.
  - Edit cron jobs using a command such as `sudo crontab -e`.
  - Add the following line to the end of the file:
```
@reboot su -c /usr/sbin/ipfm root 2>&1 > /dev/null
```
  - Save the changes (`Ctrl`+`X` to exit, then `Y` to confirm save, then `Enter` to accept the existing filename).

    :information_source: Note: you can start IP Flow Meter manually with a command such as `sudo nohup ipfm >/dev/null &`.
  
- [ ] **Perform the final check.**

  Once the blockchain is completely synchronized, the node can be shut down and disconnected from power. Power it up occasionally and let it re-sync so that it stays reasonably up-to-date for when it is needed.
