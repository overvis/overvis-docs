# Memory card flashing

The OPCB firmware manual update or a full factory reset requires uploading a new firmware image to
the SD memory card. All current firmware images can be found on the releases page:
https://github.com/overvis/opcb-release/tags

<!-- TODO: correct release page url -->

**ATTENTION! Please make sure the hardware name word of the downloaded image file matches your
device (e.g. `opipc`). The installed version displayed at the bottom of the device web interface
has the same hardware name word.**

**ATTENTION! Writing the image to the memory card deletes all data on the card, including its
settings. Make sure all the necessary data is copied. Please also make sure the device's private
key is readable on the OPCB label. The key may also be copied form the web interface state page.**

1. Download the latest image from the release page.
2. Remove the SD card from the product.
3. Write the image to the existing or new SD card .
4. Insert the freshly imaged SD card into the product.
5. Connect OPCB to the Internet using Ethernet cable and power on the porduct.
6. Connect to the product's web interface through the Wi-Fi Access Point (using the product label).
7. If necessary, configure the OPCB Internet access through the Ethernet cable.
8. Open **"Manufacturing"** page and enter the device's private key from the label, to restore it.
9. Re-configure the device's settings that are required for operation in your conditions.
