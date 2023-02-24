# Recover After Failure

- [ ] **Before you start.**

  The following instructions assume the backup hardware was previously setup per *Umbrel Setup* instructions. (It should have Bitcoin Node installed, but no Lightning Node yet.) The instructions below include icons indicating the following warning levels:
  - :rotating_light: Money could be lost if warning is not heeded.
  - :warning: Problems will result if warning is not heeded.
  - :information_source: Important information to consider.

- [ ] **Assess the damage and recover any critical data.**

  - LNbits database backup called `lnbits-db.tar.gpg` (automatically saved to monitoring server by the node's monitoring script)

    :information_source: This file does not actually contain funds. It only contains accounting data for member wallets.

    :warning: Without this file, you will have to manually return funds to members based on personal or other records.

- the passphrase to decrypt LNbits database backup

    :information_source: This is hard-coded into the monitor.sh script (should be stored with the failed node's Umbrel password).

- the backup node’s Umbrel password

Once these items have been located and secured, proceed to the next step.

- [ ] **Power down the failed node.**

  Once all the necessary recovery information has been located and secured, take steps to ensure that the old node cannot be restarted again with a henceforth conflicting configuration. For example, shut it down (if it is running) and remove the SD card, which you will probably want to reinitialize later to prepare the to-be-recycled node as a fresh backup node. Until the data on the old node is rendered inaccessible, it must be kept powered off to avoid interacting with the backup recovery process.

  - Shut down the failed node.
  - Remove the SD card from the failed node.

  :rotating_light: If the old node is restarted after the recovery process is begun, funds will be lost!

- [ ] **Disable the monitoring script.**

  This should be done as follows:
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@192.168.0.20` (where 192.168.0.20 is the backup node’s existing IP address).

    :information_source: If this is the first time you are connecting, you must accept the secure fingerprint.

  - When prompted, enter the backup node’s Umbrel password.
  - Delete or rename the custom monitoring script using a command such as `rm monitor.sh`.

    :warning: If this is not done, the script could later overwrite important backup files.

- [ ] **Set the backup node’s IP address to the production node IP address.**

  This should be done as follows, using the same SSH terminal opened above:
  - Edit the DHCP configuration using the command `sudo nano /etc/dhcpcd.conf`.

    :information_source: You will be prompted for the password again.

  - Look for the following section near the bottom and uncomment/edit the appropriate lines as follows:
```
# Example static IP configuration:
interface eth0
static ip_address=192.168.0.10/24
#static ip6_address=xxxx:xxxx:xxxx:xxxx::ff/64
static routers=192.168.0.2
static domain_name_servers=8.8.4.4 8.8.8.8 xxxx:xxxx:xxxx:xxxx::1
```

  This assumes `192.168.0.10` is the desired static IP address of the production Umbrel node. Users’ apps would already be configured for it.
  - Save the file (`Ctrl`+`X` to exit, then `Y` to confirm save, then `Enter` to accept the existing filename).

    :information_source: The IP address changes will not take effect until restart.

  - Close the terminal with the `exit` command.
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.20](http://192.168.0.20) (the backup node’s old IP address, which is still in effect.)

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the same password as above.
  - Restart the node by going to Settings and selecting `RESTART`.

- [ ] **Update the SSH key.**

  This can be done as follows:
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@192.168.0.10` (where 192.168.0.10 is the now-effective production IP address).

    :information_source: If this is the first time you connect to this IP address, you must accept the secure fingerprint.

    :information_source: If you have previously connected to the old production node, you must accept the new fingerprint.

  - When prompted, enter the password.

- [ ] **Update the Bitcoin Node configuration.**

  For the production operation, Bitcoin needs to be configured to connect to the Internet. (As a backup node, it was configured to connect instead only to the former production node.) Follow these steps:

  - In the SSH terminal opened above, edit the bitcoin configuration using a command such as `sudo nano ~/umbrel/app-data/bitcoin/data/bitcoin/bitcoin.conf`.

    :information_source: You will be prompted for the password again.

  - Remove the line `connect=192.168.0.10` (assuming this is the IP address of the old primary node).
  - In its place, add the following lines:

```
whitelist=192.168.0.15
whitelist=192.168.0.20
```

  :information_source: This will prevent the Bitcoin Node from suspecting DoS attacks from those IP addresses and blocking them.

  - Save the file (`Ctrl`+`X` to exit, then `Y` to confirm save, then `Enter` to accept the existing filename).

    :information_source: The settings will not take effect until the Bitcoin Node is restarted.

    :warning: The configuration will get reset if `RESTORE DEFAULT SETTINGS` is clicked on the Bitcoin Node Advanced Settings page.

  - Close the terminal with the `exit` command.
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.10](http://192.168.0.10) (the node’s new IP address).

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the same password as above.
  - Open the Bitcoin Node app.
  - Go to the `···` Advanced Settings page.
  - For ordinary purposes, the following settings might be recommended as of the time of writing:
    - Outgoing Connections to Clearnet Peers: On
    - Outgoing Connections to Tor Peers: On
    - Connect to all Clearnet Peers over Tor: Off
    - Outgoing Connections to I2P Peers: On
    - Incoming Connections: Off

      :warning: If incoming connections are allowed, Internet usage will be subject to the demand of the Bitcoin Network and can quickly exhaust our monthly contingent.

    - Cache Size (MB): 450
    - Replace-By-Fee (RBF) for All Transactions: Off
    - Prune Old Blocks: Off
    - Network: mainnet

      :rotating_light: Do not deviate from these or default settings without a clear understanding of the impact.

  - Restart the node by clicking `SAVE AND RESTART BITCOIN NODE`.

    :warning: The configuration items set via SSH will get reset if `RESTORE DEFAULT SETTINGS` is clicked.

  - Wait for the Bitcoin Node to restart. Once it does, you should see the synchronization proceed at a speed dependent on connectivity to other nodes on the Internet.
  - Wait for synchronization to reach 100%. This can take a while depending on network factors and how long it has been since the last synchronization.

- [ ] **Install the Lightning Node software in preparation for restoring from backup.**

  Follow these instructions carefully.
  - Before proceeding, ensure the Bitcoin Node not only shows as 100% synchronized, but that there is no longer an indication that the block count is less than the expected total.
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.10](http://192.168.0.10) (the node’s new IP address).

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the same password as above, if prompted.
  - Go to the App Store.
  - Search for Lightning Node and open its information page.
  - Click `INSTALL` to install the Lightning Node app.
  - Click `CONTINUE` on the prompt to confirm that Bitcoin Node is already installed.
  - Wait for the installation to complete. As indicated, it might take a while.

- [ ] **Recover the Bitcoin balance.**

  - When the installation is finished, open the Lightning Node app.
  - On the welcome screen, select `RECOVER YOUR PREVIOUS NODE`.
  - Enter the 24 secret seed words in the indicated order.
  - Click `NEXT`.

    :information_source: If the next button is not clickable, change the cursor focus first.

  - When asked if you had any payment channels open on your previous node, click `YES`.
  - You will be informed that the Lightning Node is currently syncing. Acknowledge this by clicking `OK`.
  - You will be cautioned about using this beta software for reckless amounts of money. Acknowledge this caution by clicking `I UNDERSTAND & AGREE`.
  - The Lightning Node page will have a progress bar indicating that it is scanning all transactions for recovery, and that it may take a while. Wait until it is complete.

    :information_source: The progress bar will remain unchanging for long periods. Be patient.

    :information_source: You will see zero or partial balances until the recovery is complete.

- [ ] **Recover the funds in Lightning channels.**

  Once the Bitcoin transaction scan is complete, you should see the Bitcoin Wallet balance restored, but the Lightning Wallet will still show zero because the channels have not yet been re-discovered.
  - On the `···` menu, select Recover channels.
  - You will be presented with a list of backup files that Umbrel has stored on cloud servers. Use the latest backup shown, or click `UPLOAD` to upload the local channel backup file.

    :rotating_light: Selecting a backup file older than the last channel open/close operation will result in loss of funds.

  - Once you load a backup, you will see your channels in the Closing state. It can take a long time to close each channel and receive the balance from that channel. Wait until all channels have closed and disappeared from the console.

  :information_source: It will take at least four blocks before channels have finished closing, maybe much longer depending on the remote side.

  :information_source: You can monitor the status as shown in the next task.

- [ ] **Monitor channel closures (optional).**

  After one block is mined, you might check the channel status and see something similar to the following:

```
umbrel@umbrel:~ $ ~/umbrel/bin/lncli pendingchannels

  *** Deprecation notice ***
  In a future version of Umbrel, 'lncli' will be removed.

  Please instead use:
  ./scripts/app compose lightning exec lnd lncli <sub-command>

{
    "total_limbo_balance": "0",
    "pending_open_channels": [
    ],
    "pending_closing_channels": [
    ],
    "pending_force_closing_channels": [
    ],
    "waiting_close_channels": [
        {
            "channel": {
                "remote_node_pub": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
                "channel_point": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx:1",
                "capacity": "xxxxxxxx",
                "local_balance": "0",
                "remote_balance": "0",
                "local_chan_reserve_sat": "0",
                "remote_chan_reserve_sat": "0",
                "initiator": "INITIATOR_LOCAL",
                "commitment_type": "ANCHORS",
                "num_forwarding_packages": "0",
                "chan_status_flags": "ChanStatusLocalDataLoss|ChanStatusRestored",
                "private": true
            },
            "limbo_balance": "0",
            "commitments": {
                "local_txid": "",
                "remote_txid": "",
                "remote_pending_txid": "",
                "local_commit_fee_sat": "0",
                "remote_commit_fee_sat": "0",
                "remote_pending_commit_fee_sat": "0"
            },
            "closing_txid": ""
        }
    ]
}
umbrel@umbrel:~ $
```

In the best of cases, after three blocks have been mined, the former Lightning Wallet balance should be restored to the Bitcoin Wallet and the balance updated accordingly.

If you examine the channel status again, you might see something similar to the following. Notice the following interesting bits of information:
- limbo balances, which indicate the amount(s) being received
- maturity_height, which indicates the block height at which the on-chain funds will be spendable (e.g. to open a new channel)
- closing_txid, which can be useful for examination in a block explorer

```
umbrel@umbrel:~ $ ~/umbrel/bin/lncli pendingchannels

  *** Deprecation notice ***
  In a future version of Umbrel, 'lncli' will be removed.

  Please instead use:
  ./scripts/app compose lightning exec lnd lncli <sub-command>

{
    "total_limbo_balance": "xxxxxxx",
    "pending_open_channels": [
    ],
    "pending_closing_channels": [
    ],
    "pending_force_closing_channels": [
        {
            "channel": {
                "remote_node_pub": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
                "channel_point": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx:1",
                "capacity": "xxxxxxxx",
                "local_balance": "xxxxxxx",
                "remote_balance": "0",
                "local_chan_reserve_sat": "0",
                "remote_chan_reserve_sat": "0",
                "initiator": "INITIATOR_LOCAL",
                "commitment_type": "ANCHORS",
                "num_forwarding_packages": "0",
                "chan_status_flags": "",
                "private": true
            },
            "closing_txid": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
            "limbo_balance": "xxxxxxx",
            "maturity_height": xxxxxx,
            "blocks_til_maturity": 1,
            "recovered_balance": "0",
            "pending_htlcs": [
            ],
            "anchor": "LIMBO"
        }
    ],
    "waiting_close_channels": [
    ]
}
umbrel@umbrel:~ $
```

As you can see, this type of close operation only requires one more block to complete. Once the next block has been mined, the Bitcoin Wallet pane will show a completed deposit for an amount similar to the limbo amount indicated earlier. Another query of the pending channel closures will indicate that the closure is complete:

```
umbrel@umbrel:~ $ ~/umbrel/bin/lncli pendingchannels

  *** Deprecation notice ***
  In a future version of Umbrel, 'lncli' will be removed.

  Please instead use:
  ./scripts/app compose lightning exec lnd lncli <sub-command>

{
    "total_limbo_balance": "0",
    "pending_open_channels": [
    ],
    "pending_closing_channels": [
    ],
    "pending_force_closing_channels": [
    ],
    "waiting_close_channels": [
    ]
}
umbrel@umbrel:~ $
```

Once all channels have closed, the process of recovering funds is complete.

- [ ] **Install the LNbits app.**

  To install the LNbits app, do the following:
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.10](http://192.168.0.10) (the node’s IP address).

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the usual password.
  - Go to the App Store.
  - Search for LNbits and open its information page.
  - Click `INSTALL` to install the LNbits app.
  - Click `CONTINUE` on the prompt to confirm that Lightning Node is already installed.
  - Wait for the installation to complete. As indicated, it might take a while.

- [ ] **Restore user wallets from backup.**

  To restore user wallets, perform the following steps:
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@192.168.0.10` (where 192.168.0.10 is the node’s IP address).
  - When prompted, enter the password.
  - Copy the backup file using a command such as `scp monitoruser@192.168.0.15:/home/monitoruser/lnbits-db.tar.gpg .`.
  - Change to the LNbits folder using a command such as `cd umbrel/app-data/lnbits/`.
  - Create a new folder for the recovery using a command such as `mkdir restore`.
  - Change to the newly-created restore folder using a command such as `cd restore`.
  - Decrypt the backup file using a command such as `gpg -d ~/lnbits-db.tar.gpg | tar xf -`.
  - When prompted, enter the passphrase.
  - Change the ownership of all the extracted files using a command such as `sudo chown root:root *`.

    :information_source: You might be prompted for the Umbrel password. If so, enter it.

  - Change back to the LNbits folder using a command such as `cd ..`.
  - Swap the restore folder for the data folder using a command such as `mv data data.old; mv restore data`.
  - Close the shell using the `exit` command.

  For the restored databases to be recognized, restart Umbrel as follows:
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.10](http://192.168.0.10) (the production node’s IP address).

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the same password as before.
  - Restart the node by going to Settings and selecting `RESTART`.
  - Verify that the wallets have been restored by visiting existing wallet URLs and checking the balances.

    :warning: Users won’t be able to spend from or deposit into their wallets until liquidity is available (see below). 

- [ ] **Cleanup LNbits restore files.**

  Perform the following steps:
  - SSH into the Raspberry Pi 4 using a command such as `ssh umbrel@192.168.0.10` (where 192.168.0.10 is the node’s IP address).
  - When prompted, enter the password.
  - Delete the LNbits backup file using a command such as `rm lnbits-db.tar.gpg`.
  - Delete the (moved) original LNbits database files using a command such as `rm -rf umbrel/app-data/lnbits/data.old`.

- [ ] **Restore the monitoring script.**

  Restore the monitoring script by copying it with a command such as `scp monitoruser@192.168.0.15:/home/monitoruser/umbrel-backup/monitor.sh .`.
  - Make the script executable using a command such as `chmod a+x monitor.sh`.
  - Make sure the script is configured to run automatically by checking the root crontab, which can be done with the command `sudo crontab -e`.

    :information_source: When prompted for the Umbrel password, enter it.

    :information_source: You might be prompted to select an editor. The nano editor will work.

  - Ensure that the following line appears in the file, adding it if necessary:

```
@reboot su -c /home/umbrel/monitor.sh umbrel 2>&1 > /dev/null
```

  - Exit using `Ctrl`+`X`. If prompted, confirm save with `Y` and `Enter` to accept the existing filename.
  - Open the Umbrel console by pointing your web browser to [http://192.168.0.10](http://192.168.0.10) (where that is the node’s IP address).

    :information_source: If the page does not load correctly on the first try, use the browser’s refresh button.

  - Login using the usual password.
  - Open the Bitcoin Node app.
  - Click the `CONNECT` button.
  - In the window that opens, find the Bitcoin Core RPC Password and copy it.
  - Go back to the shell and edit the monitoring script using a command such as `pico monitor.sh`.
  - Look for the RPC_PW variable assignment:

```
# Bitcoin Core RPC Password
RPC_PW="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

  - Update the value in quotation marks by deleting it and pasting the value previously copied from Umbrel.

  For the monitoring script to run, the node needs to be re-started.
  - Restart the node by going to Settings and selecting `RESTART`.

  Alternatively, the script can be started manually without restarting Umbrel. This can be useful when editing, testing, and/or debugging the script:
  - Find the running instance using a command such as `ps -aux|grep monitor.sh`.
  - Kill the running instance (if any) using a command such as `kill -9 <pid>` where <pid> is the process id of the running process.
  - Start the script anew. If you want to see any error or other output, simply use `./monitor.sh`. If you wish to run it permanently even if the shell is closed or the connection is lost, use `nohup ./monitor.sh 2>/dev/null &`.

- [ ] **Install Electrs and Mempool.**

  Electrs is required for Mempool, and Mempool is the Bitcoin block explorer. Follow the usual method of installing from the App Store. No special configuration is required for these.

- [ ] **Open new channels to restore liquidity.**

  Once this is done, the node is operation again for all users.
