# Umbrel Setup

- [ ] **Make sure the hardware is ready.**

  The initial steps are based on [umbrel.com](https://umbrel.com/).

  You need all these (or equivalent) parts assembled:
  - Raspberry Pi 4 (8GB recommended)
  - SSD (2TB recommended)
  - Powered dock for the SSD
  - MicroSD card (128 GB recommended)
  - Raspberry Pi 4 power supply (the official one is recommended)
  - Ethernet cable to connect to LAN
  - Case/housing
  Additionally, the setup should be powered by a UPS in case of power failures.
  
  :warning: In case of lightning storms, perform a graceful shut down of the node and unplug the UPS to avoid damage until risk passes.

  :warning: It is advisable to have the MAC addresses of the device. Record it at the earliest opportunity.

- [ ] **Download Umbrel OS.**

  [https://download.umbrel.com/umbrel-os.zip](https://download.umbrel.com/umbrel-os.zip) (931 MB)
  
  Make sure to use the latest version.

- [ ] **Download Raspberry Pi Imager for your Windows PC.**

  [https://downloads.raspberrypi.org/imager/imager_latest.exe](https://downloads.raspberrypi.org/imager/imager_latest.exe) (19 MB)
  
  Make sure to use the lastest version.

- [ ] **Plug the SD card into your Windows PC.**

  You might need a size adapter. The card should be detected automatically when plugged in. If prompts to format it and/or folder windows pop open, just cancel and/or close them.

- [ ] **Flash Umbrel OS.**

  Open Raspberry Pi Imager and flash the OS to the SD card as follws:
  - Under CHOOSE OS, select Use custom, then browse to the Umbrel zip file downloaded earlier.
  - Under CHOOSE STORAGE, select the SD card.
  - Finally, click WRITE and confirm.

  Again, some prompts and/or windows might pop open at the beginning and end of the writing process. Ignore, close, or cancel them.
  - When the writing and verification is finished, remove the SD card.

- [ ] **Insert the microSD card into the Raspberry Pi.**

  Insert the microSD card while the power is off.

- [ ] **Connect the SSD.**

  Put the SSD in its enclosure, and plug it into any of the two USB 3.0 ports (blue colored) on the Raspberry Pi.
  
  :rotating_light: ATTENTION: Any existing data on the SSD will automatically be deleted when you turn on the Raspberry Pi. (The drive will be formatted automatically.)

- [ ] **Connect to your router.**

  Connect one end of the ethernet cable to the Raspberry Pi and the other end to any vacant port on your internet router, hub, or access point.

- [ ] **Power up.**

  Connect the power supply to the Raspberry Pi to turn it on.

- [ ] **Find the IP address.**

  After 5 minutes, Umbrel should be accessible at [http://umbrel.local](http://umbrel.local) on any device that is connected to the same network as the Raspberry Pi. However, `.local` addresses do not always work, and you might have to find the DHCP-assigned IP address another way, such as by comparing the Pi's MAC address to the DHCP server's client list or to the output of the `arp -a` command.

- [ ] **Access the Umbrel control panel.**

  Open the Umbrel node using either [http://umbrel.local](http://umbrel.local) or by typing in the discovered IP address into your browser's address bar.

- [ ] **Set up Umbrel.**

  When the welcome screen appears, click START and fill in the necessary information:
  - Enter a name you will be greeted with upon login (it is not used for anything else).
  - For the password, use a secure tool to generate a new strong password.

    :rotating_light: ATTENTION: Store this password in a secure place, because there is no other way to access the Umbrel node or reset the password if it is lost!

- [ ] **Set a permanent IP address.**

  :information: For the sake of this document, we will assume the desired fixed IP address should be 192.168.0.20 and the gateway is 192.168.0.2.

  You could do this in the DHCP server's configuration, or as follows:
  - SSH into the Raspberry Pi using a command such as `ssh umbrel@umbrel.local` (use the IP if needed).
  - When prompted, use the password selected above.
  - Edit the DHCP configuration using the command `sudo nano /etc/dhcpcd.conf`. Look for the following section near the bottom and uncomment/edit the appropriate lines (especially static ip_address and static routers) as follows:
```
# Example static IP configuration:
interface eth0
static ip_address=192.168.0.20/24
#static ip6_address=xxxx:xxxx:xxxx:xxxx::ff/64
static routers=192.168.0.2
static domain_name_servers=8.8.4.4 8.8.8.8 xxxx:xxxx:xxxx:xxxx::1
```

- [ ] **Restart Umbrel OS.**

  Go to settings (the gear icon on the bottom toolbar) and click RESTART.

- [ ] **Reopen the Umbrel control panel.**

  Close the browser tab and type in the new static IP address in a new browser tab. Login to Umbrel.

- [ ] **Install “Bitcoin Node”.**

  Install Bitcoin Node by following the prompt (or by using the App Store icon in the bottom toolbar).

- [ ] **Configure the initial block download (IBD) for LAN only.**

  To save downloading the hundreds of gigabytes of blockchain data over the Internet, you can sync the new node by connecting to another peer that is already synced and on the same LAN. To do this, do the following:
  - Open the Bitcoin Node app, go to `···` Advanced Settings, and disable Connect to all Clearnet Peers over Tor, then click SAVE AND RESTART BITCOIN NODE.
  - Wait for it to show some peer connections.
  - SSH into the Raspberry Pi using `ssh umbrel@192.168.0.20` (or whatever its IP address is).
  - Run `sudo nano umbrel/app-data/bitcoin/data/bitcoin/bitcoin.conf` or similar to edit the bitcoind configuration file.
  - Add the line `connect=192.168.0.20` (assuming this is the IP address of a synced node on the LAN). Save and exit.

    :warning: The configuration will get reset if `RESTORE DEFAULT SETTINGS` is clicked on the Bitcoin Node Advanced Settings page.
  - In the Umbrel settings, select RESTART.
  Once Umbrel has restarted, you can login again and open the Bitcoin Node control panel and see that it will show only a single peer connection over Clearnet (without Tor), and now the IBD will happen over the LAN with that peer. NOTE: If it wont connect, the other node might need to have a configuration line such as `whitelist=192.168.0.20` added and the node restarted.

  :warning: The configuration will get reset if `RESTORE DEFAULT SETTINGS` is clicked on the Bitcoin Node Advanced Settings page.

- [ ] **Configure secure SSH connection to monitoring server.**

  This will allow scripts on the node to report data to a designated server securely.
  - SSH into the Raspberry Pi using a command such as `ssh umbrel@192.168.0.20`.
  - Create a new private/public key pair using the command `ssh-keygen`, accepting all defaults.
  - Copy the public key to the server using a command such as `ssh-copy-id monitoruser@192.168.0.15`, where 192.168.0.15 is the IP address of the monitoring server. When prompted, enter that server password.

  Using a command such as `ssh monitoruser@192.168.0.15`, you (or scripts) can now SSH from the Umbrel node to the monitoring server without a password. This will enable the next step.

- [ ] **Add monitoring script.**

  - Run the command `pico monitor.sh` to create the monitoring script. Paste in the following code:
```
(Coming soon...)
```

- [ ] **Perform the final check.**

  Once the blockchain is completely synchronized, verify that the configuration is as it should be.
  - If this is destined to be a production node, proceed to the *Recover After Failure* checklist.
  - If this will remain as a backup node, then its main job is to maintain a copy of the blockchain and no configuration change or further apps are needed at this point. It will continue to keep its blockchain up to date through the primary node over the LAN without consuming Internet bandwidth. It can be shut down and periodically brought online to keep the blockchain semi-current as a backup. (If the primary node fails at a later date, the backup node can then be reconfigured by following the *Recover After Failure* checklist to add more apps, restore wallets, etc., to replace the failed node.)
