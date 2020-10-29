# Overvis Connect Protocol Version 1

## Features and extensions directory

* [Basic protocol](overvis-connect.md) _(approved)_
    * `0000` Handshake
    * `0001` Keep Alive
* [`EXT0001` Device Parameters](ext0001-device-params.md) _(approved)_
    * `0002` Read Parameters
    * `0003` Write Parameters
* [`EXT0002` Device State](ext0002-device-state.md) _(approved)_
    * `0004` Read State
* [`EXT0003` Device Journal](ext0003-device-journal.md) _(testing)_
    * `0005` Read Journal
    * `0006` Clear Journal
* [`EXT0004` Device Clock](ext0004-device-clock.md) _(testing)_
    * `0007` Get Time
    * `0008` Set Time
* [`EXT0005` Firmware](ext0005-firmware.md) _(approved)_
    * `0009` Get FW File Version
    * `000A` Clear FW File
    * `000B` Send FW File
    * `000C` Start FW Update Process
* [`EXT0006` Basic Events](ext0006-basic-events.md) _(testing)_
    * `000D` Read Events
    * `000E` Write Events
    * `000F` Delete Events
    * `0010` Delete All Events
* [`EXT0007` Sensitive Information Check](ext0007-sensitive-info-check.md) _(approved)_
    * `0011` Confirm Value Correctness
* [`EXT0008` Get Device ID](ext0008-get-device-id.md) _(approved)_
    * `0012` Get Device ID
* [`EXT0009` DH Public Data for Shared Secret Exchange](ext0009-dh-public-data-sse.md) _(approved)_
    * `0013` Public Data for New Shared Secret
* [`EXT000A` Device Reboot](ext000a-device-reboot.md) _(approved)_
    * `0014` Reboot Device
* [`EXT000B` Next Reconnect](ext000b-next-reconnect.md) _(testing)_
    * `0015` Set Next Reconnect
* [`EXT000C` Shared Secret Authentication](ext000c-shared-secret-auth.md) _(approved)_
    * `0016` Device-Server Shared Secret Authentication
* [`EXT000D` Send Wrapped Packet](ext000d-wrapped-packet.md) _(approved)_
    * `0017` Exchange Wrapped Packets
* [`EXT000E` Encrypted Packet AES 256 CBC](ext000e-encrypted-packet-aes256cbc.md) _(testing)_
    * `0018` Send Encrypted Request
* [`EXT000F` Asynchronous Process Result](ext000f-asynchronous-result.md) _(approved)_
    * `0019` Get Asynchronous Process Result    
* [`EXT0010` Encrypted Packet Gamma](ext0010-encrypted-packet-gamma.md) _(approved)_
    * `0018` Send Encrypted Request
* [`EXT0011` EdDSA Authentication](ext0011-eddsa-auth.md) _(discussion)_
    * `001A` Start Device-Server EdDSA Authentication
    * `001B` Confirm Device-Server EdDSA Authentication
    * `001C` Create New EdDSA Secret
* [`EXT0012` Plain Password](ext0012-plain-password.md) _(draft)_
    * `001D` Send Plain-Text Password
* [`EXT0013` File System Read](ext0013-file-system-read.md) _(discussion)_
    * `001E` Read Access to File
    * `001F` Read from File
* [`EXT0014` File System Navigate](ext0014-file-system-navigate.md) _(discussion)_
    * `0020` Access to Directory
    * `0021` Next List of Directory
* [`EXT0015` File System Write](ext0015-file-system-write.md) _(discussion)_
    * `0022` Write Access to File
    * `0023` Write to File
* [`EXT0016` File System Manage](ext0016-file-system-manage.md) _(discussion)_
    * `0024` Rename/Move File System Element
    * `0025` Remove/Delete File System Element
    * `0026` Close Access to File System Element
* [`EXT0017` File System Service](ext0017-file-system-service.md) _(draft)_
    * `0024` Rename/Move File System Element
    * `0025` Remove/Delete File System Element
    * `0026` Close Access to File System Element
    * `0027` Copy File System Element
    * `0028` Initialize/Format File System

# Document statuses

* _Draft_ - the document is not ready for discussion.
* _Discussion_ - ocument under discussion, pending further edits.
* _Testing_ - the document is under implementation and testing, edits are possible. Devices with implementation are not available for sale.
* _Approved_ - the document is ready, no edits are possible. Devices with implementation are now on sale. Approved documents are not deleted, they can only be marked as outdated. When approving a document, it is necessary to mark the date of approval in the meta-header.
* _Outdated document_ - the document is no longer used, edits are not possible, devices with implementation are released for sale, but new ones are not produced. Outdated documents are not deleted.
