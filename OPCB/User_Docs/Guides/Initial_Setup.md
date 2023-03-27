# Initial Setup

The unconfigured OPCB creates an access point `OPCB_XXXXXX`(where XXXXXX - are the last 6 digits of
the MAC address on the product label) after it is turned on and initialized.

To configure, connect to this access point without the password, then enter the address
`setup.overvis.com` (or `192.168.4.1`) in the browser.

The OPCB state page will open. Select the `Settings` section from the menu in the top right corner.

The OPCB settings page will open as shown in the image.

![OPCB Setting page, general settings](./images/ui-main-screen.png)

By selecting the desired tabs and subsections in them, the OPCB should be configured.

_If the device is to work via a cloud server, you must configure one or more network interfaces in
the `Network` tabs through which the OPCB can access the Internet. Then set the address of the
cloud server on the `Overvis` tab, if necessary. After correctly setting the Internet access
parameters and saving the settings, OPCB will establish communication with the cloud server.
Further configuration can be done through the cloud server._

After finishing the setting, save the parameters with the button `Save settings`.
