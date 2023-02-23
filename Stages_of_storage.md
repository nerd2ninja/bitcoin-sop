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

**Do not use a signing device company's software wallet to broadcast transactions. Even when open source, a company specific software wallet is often too niche for the community to review and scrutinize**

## Cold Card
https://coldcard.com/ **Requires manual dismantling of wireless devices**

Verify firmware https://github.com/Coldcard/firmware

## Jade
https://blockstream.com/jade **Bluetooth can be turned off but this wireless device is difficult to remove**

Verify firmware by performing file hashes https://github.com/Blockstream/Jade **Because Jade checks that the firmware is signed, pushing the compiled software will turn the device into a brick. Thus you rely on file hashing to verify. This can be comprimised if the hashing method is broken (contains collisions). If collisions in the file hash for Jade is found please report to Jade**

## Bitbox
https://shiftcrypto.ch/bitbox02/bitcoin-only/ **note: This company posts misinformation about air gaps**

Verify, compile, and install firmware https://github.com/digitalbitbox/bitbox02-firmware
