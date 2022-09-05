# Initial Setup

The unconfigured product creates an access point `OPCB_XXXXXX`(where XXXXXX - is the last 6 digits
of the MAC address on the product label) after it is turned on and initialized.

To configure, connect to this access point with the password `12345678`, then enter the address
`setup.overvis.com`(or `192.168.4.1`)in the browser.

The setup page will open, as shown in the figure.

![OPCB-221 Setting page, general settings](./images/ui-main-screen.png)

By selecting the desired tabs and subsections in them, the OPCB-221 should be configured.

_If the device is to work via a cloud server, you must configure one or more network interfaces in
the `Network` tabs through which the OPCB-221 can access the Internet. Then set the address of the
cloud server on the `Overvis` tab, if necessary. After correctly setting the Internet access
parameters, saving the settings and restarting the product, communication with the cloud server
will be established. Further configuration can be done through the cloud server._

After finishing the setting, save the parameters with the button `Save settings`.

Then on the `Actions` tab click the `Restart device` button to restart OPCB-221 and apply the
settings.
