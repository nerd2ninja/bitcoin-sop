# Introduction

You will notice no information in this document contains a set of "Do this, don't do that". Everything is a matter of trade offs. Please read throughly and choose the trade offs that work best for your use case

# Stages of Storage

Explainer for the Stages of Bitcoin self-custody from lightning to cold storage:
https://armantheparman.com/bitcoin-storage-get-better/

# Cold Storage

A guide for stages of cold storage based on net worth
https://yeticold.com/

A verbose technical guide for securing Bitcoin in cold storage
https://glacierprotocol.org/

Anti-destruction (fire corrosion etc) metal plate reviews
https://jlopp.github.io/metal-bitcoin-storage-reviews/

# Off the Shelf or Do it Yourself Signing devices

Darthcoin's build your own cold wallet guide https://darthcoin.substack.com/p/build-your-hodl-btc-cold-wallet

402 Payment Required's build your own cold wallet guide https://www.youtube.com/watch?v=-tLoiLo5fT8&t=115s

Seed signer
https://seedsigner.com/

Krux
https://selfcustody.github.io/krux/

# Prebuilt Signing Devices

**Do not use a closed source software wallet. A signing device specific software wallet can sometimes have features which do extra verification that is specific to that signing device. The trade off however is that company specific software wallets even when open source are often too niche for the greater Bitcoin community to review and scrutinize**

## Cold Card
https://coldcard.com/ **Requires manual dismantling of wireless devices**

Verify firmware https://github.com/Coldcard/firmware

Cut wireless transmission capability
![cut NFC](https://blog.coinkite.com/images/posts-img/COLDCARD-mk4-pcb.png)

Image to the right under the big port has a wire under the lettering "NFC". Cut that wire to prevent the NFC from recieving power, thereby making impossible to exfiltrate data using that wireless device in the case of a malicious firmware update (which would only happen if you didn't compile the firmware from source anyway)

## Jade
https://blockstream.com/jade **Bluetooth can be turned off but this wireless device is difficult to remove**

Verify firmware by performing file hashes https://github.com/Blockstream/Jade **Because Jade checks that the firmware is signed, pushing the compiled software will turn the device into a brick. Thus you rely on file hashing to verify. This can be comprimised if the hashing method is broken (contains collisions). If collisions in the file hash for Jade is found please report to Jade**

## Bitbox
https://shiftcrypto.ch/bitbox02/bitcoin-only/ **note: This company posts misinformation about air gaps**

Verify, compile, and install firmware https://github.com/digitalbitbox/bitbox02-firmware
