# Reference

[< Return to the table of contents](../README.md)

Overvis visualization source code is an HTML snippet. This snippet code is not processed on the server-side and is embedded inside the Overvis page HTML as-is. It can contain JavaScript inside `<script>` tags, CSS inside `<style>` tags, or SVG images inside `<svg>` tags. It should not contain `<!...>`, `<html>`, `<head>`, `<body>` or other header tags because they are already present on the Overvis page.

There are several extension tags and attributes that are added by the JavaScript to the Overvis page for simplifying visualization data binding. The following reference will list all such additions. Visualization code is not required to use them and can directly call Overvis API instead.

In the reference below, the following syntax: `update-period-sec="{{number}}"` means that the double curly braces should be replaced by the referenced value type. In this example, the correct usage will be something like this: `update-period-sec="60"`.

## Refresh process

After the visualization is loaded, it gathers all used data bindings from the source code and starts the automatic data refresh process. Visualization data is being refreshed every 10 seconds by default.

The next refresh cycle doesn't start before the previous one is finished. The length of the refresh cycle depends on how many parameters are being read from the devices and the connection quality. So, if there are many parameter readings to be requested at once from the slow channel, the refresh cycle may take more time than planned. To optimize the refresh cycle time, prefer loading parameter values from the Overvis database instead of reading them from the devices directly.

## Visualization settings

The first `<svg>` tag on the page or any tag with the class `vis-settings` can contain the following additional attributes:

Attribute | Default | Description
----------|---------|------------
`update-period-sec="{{number}}"` | 10 | Refresh period of the visualization in seconds. See [refresh process.](#refresh-process) `0` - refresh immediately after the previous refresh cycle is finished.
`refresh-button="{{yes/no}}"` | yes | `no` - hides the refresh button in the top-left corner of the visualization.
`use-vis-data="{{yes/no}}"` | no | `yes` - enables the initial loading of the visualization strings table data. See [visualization strings table.](#visualization-strings-table)

## Additional JavaScript functions

Those functions are added to the global `window` object and accessible everywhere in the code:

Function | Description
---------|------------
`preInitViz(cb)` | This function is called after the visualization source code is embedded in the page, but before the references and aliases are being resolved. This function can add additional binds or modify visualization source code. `cb` is a callback function that should be invoked at the end of `preInitVis`.
`onVizUpdate(data)` | This function is invoked after any bound data is received (at least after each refresh cycle). `data` is an object with references as keys.
`writeParamValue(ref, val, cb, refreshAll)` | Send parameter value to the device. `ref` - parameter reference; `val` - value as a string; `cb` - a function that should be called after the writing process will be finished; `refreshAll` - boolean, if true - refresh all data on the visualization, if false - refresh only the changed parameter.

## Referencing objects

All referenced objects should exist in Overvis. For example, you can't reference the network by IP/MAC address if the network with this IP or MAC is not created under your account.

### Networks can be referenced in one of the following ways:

Syntax | Example | Description
-------|---------|------------
`#{{number}}` | `#123` | By system ID.
`[{{slug string}}]` | `[my-network]` | By the slug string. Slug is a unique code assigned to each object. It can be taken from the URL of the network page in Overvis.
`~{{alias name string}}` | `~someNetwork` | Using alias. Alias with this name should be provided by the `alias` tag in visualization code.
`{{ipv4 address}}` | `123.66.11.34` | By network IP address (without port) for networks with a direct connection.
`{{mac address}}` | `12-34-56-78-90-ab` | By network MAC address for networks with a backward connection. Network MAC can be seen on the network settings page.

### Devices can be referenced in one of the following ways:

Syntax | Example | Description
-------|---------|------------
`#{{number}}` | `#87875` | By system ID.
`[{{slug string}}]` | `[my-device]` | By the slug string. Slug is a unique code assigned to each object. It can be taken from the URL of the device page in Overvis.
`~{{alias name string}}` | `~someDevice` | Using alias. Alias with this name should be provided by the `alias` tag in visualization code.
`{{network reference}}>{{device address}}` | `12-34-56-78-90-ab>111` | By the device Modbus unit id (address) inside the network.

### Device parameters can be referenced in one of the following ways:

Syntax | Example | Description
-------|---------|------------
`#{{number}}` | `#1234567` | By system ID.
`[{{slug string}}]` | `[my-param]` | By the slug string. Slug is a unique code assigned to each object. It can be taken from the URL of the parameter settings page in Overvis.
`~{{alias name string}}` | `~someParam` | Using alias. Alias with this name should be provided by the `alias` tag in visualization code.
`{{device reference}}>{{address}}` | `12-34-56-78-90-ab>111>123` | By the parameter address inside the device. Note, this parameter should exist in the device parameters list in Overvis. `{{address}}` can also contain additional specifications, see below.

Parameter address has the following extended syntax:

```
{{registerAddress}}.{{startBitAddress}}-{{endBitAddress}}{{registerKind}}
```

`{{registerKind}}` can be: `c` - coil, `d` - discrete input, `i` - input. Omitting it means holding register.

Few examples:

Example | Description
--------|------------
`123` | Any holding parameter that starts with the address 123.
`123c` | Coil bit paramter with the address 123.
`123.4` | 4th bit from the register 123.
`123.4-5i` | Bits 4-5 from the input register 123.

### Visualizations can be referenced in one of the following ways:

Syntax | Example | Description
-------|---------|------------
`#{{number}}` | `#123` | By system ID.
`[{{slug string}}]` | `[my-visualization]` | By the slug string. Slug is a unique code assigned to each object. It can be taken from the URL of the visualization page in Overvis.
`~{{alias name string}}` | `~someVis` | Using alias. Alias with this name should be provided by the `alias` tag in visualization code.

## Display parameter value: `param-value="..."` attribute

Any container tag in HTML/SVG code has the additional possible attribute:

```
param-value="{{paramRef}}:{{options}}"
```

This attribute specifies that the contents of this tag should be replaced by the value of the parameter referenced by `{{paramRef}}`. By default, this value will be taken from the Overvis database of last known parameter values.

`{{options}}` is a comma-separated list of flags. Currently supported flags:
* `r` - read data directly from the device,
* `p{{number}}` - display data with specified precision.

Also, the following attribute can be used in conjunction with `param-value`:

```
param-value-mod="{{evalExpr}}"
```

It applies the quick mathematical expression `evalExpr` where the current value will be passed as `x`.

Few examples:

Example | Description
--------|------------
`<div param-value="12-34-56-78-90-ab>111>123">loading...</div>` | Placeholder text `loading...` will be replaced with the value of parameter 123 of the device 111 in the network 12-34-56-78-90-ab. Value will be taken from the database, so this parameter should be marked as tracked in the system.
`<div param-value="12-34-56-78-90-ab>111>123:r" />` | The value of the parameter will be read from the device on each refresh. The parameter can be untracked.
`<div param-value="12-34-56-78-90-ab>111>123:r,p3" />` | Real-time value of the parameter with 3-digits precision.
`<div param-value="12-34-56-78-90-ab>111>123" param-value-mod="x*2+1" />` | The value of the parameter will be multiplied by 2 and increased by 1.

### Real-time readings vs. last known readings

By default, the value of the parameter is taken from the Overvis database, not read from the device. This makes visualization refresh much faster, but it has the following consequences:

* Only tracked parameter values are stored in the database.
* The update interval of the value depends on the update of the device background readings (specified in the device settings) and not on the visualization refresh period.
* If there is no network connection, the last known value will still be displayed.
* If the parameter is untracked or has never been read yet, `NOLR` will be displayed instead.

If you want to perform an actual reading of the device parameter, set the `:r` option at the end. In this case:

* It doesn't matter if the parameter is tracked or untracked.
* The value of the parameter is read from the device after each visualization refresh cycle (usually every 10s).
* If the network connection is missing, `NORV` is displayed.

### Error indicators

Several special codes are returned by the server on various exceptions. You may see them instead of the parameter value:
* `NON` - parameter network does not exist.
* `NOP` - parameter does not exist.
* `NOLR` - no last reading for the parameter in the database (parameter has never been read). That usually means that this parameter is untracked. Mark it as tracked or use real-time value reading.
* `NORV` - error reading parameter value from the device. Try to read it on the device parameters list page to see the actual error.

## React on the parameter value change: `on-param-value="..."` attribute

Any tag in HTML/SVG code has the additional possible attribute:

```
on-param-value="{{paramref}}:{{condition}}:{{property}}:{{valueOn}}:{{valueOff}}:{{options}}"
```

This attribute toggles the property or style specified by `{{property}}` between `{{valueOn}}` and `{{valueOff}}` depending if the parameter referenced by `{{paramRef}}` satisfies the `{{condition}}`.

`{{condition}}` can be one of the following:

* `>value`
* `<value`
* `=value`
* `!=value`
* `>=value`
* `<=value`
* or `{evalExpr}` where `evalExpr` in curly braces can be any boolean JS expression with current parameter value provided as `x`. See examples below.

If the condition is satisfied, the property *and* the style by the name provided by `{{property}}` will be set to `{{valueOn}}`. Otherwise it will be set to `{{valueOff}}`.

`{{options}}` is a comma-separated list of flags. Currently supported flags:
* `r` - read data directly from the device. By default it is taken from Overvis database of last known parameter values.

Few examples:

Example | Description
--------|------------
`<div on-param-value="12-34-56-78-90-ab>111>123:>10:color:red:green">pressure</div>` | Set text style color to red if parameter value is greater than 10. Otherwise - green. Value is taken from Overvis database, so the parameter should be tracked.
`<div on-param-value="12-34-56-78-90-ab>111>123:<20:display:block:none:r">alert!</div>` | Text `"alert!"` will be visible if parameter value is less than 20. Value is read from the device in real-time. By default (until the value is loaded), text is hidden.
`<text on-param-value="12-34-56-78-90-ab>111>123:{x<10\|\|x>20}:color:red:green:r">pressure</text>` | If parameter value is less than 10 or larger than 20, text `"pressure"` will be displayed in red.

## React on the connection status change: `on-net-connection="..."` attribute

Any tag in HTML/SVG code has the additional possible attribute:

```
on-net-connection="{{netRef}}:{{property}}:{{valueOn}}:{{valueOff}}"
```

This attribute toggles the property or style specified by `{{property}}` between `{{valueOn}}` and `{{valueOff}}` depending if network referenced by `{{netRef}}` has connection or not.

Example:

Example | Description
--------|------------
`<div on-net-connection="12-34-56-78-90-ab:display:none:block">disconnected!</div>` | The text `"disconnected!"` will be visible only if the connection with the network that has MAC address `12-34-56-78-90-ab` is lost.

## Recact on the click action: `on-click="..."` attribute

Any interactable tag in HTML/SVG code has the additional possible attribute:

```
on-click="{{action}}"
```

It specifies the action which should be taken when user clicks or taps on this element.

`{{action}}` can be one of the following:

* `goto:param:{{paramRef}}`

    Open history of the parameter readings.

* `goto:params:{{paramRef}},{{paramRef}},...`

    Open comparison of parameter readings.

* `goto:device:{{deviceRef}}`

    Open device parameters list page.

* `goto:network:{{networkRef}}`

    Open network page.

* `goto:vis:{{visualizationRef}}`

    Open the visualization.

* `toggle-param:{{paramRef}}:{{defaultValue}}:{{valueOn}}:{{options}}`

    If the parameter `{{paramRef}}` has value different from `{{defaultValue}}` then set it to `{{defaultValue}}`, otherwise set it to `{{valueOn}}`.

    Example: `<button on-click="toggle-param:12-34-56-78-90-ab>1>123:0:1:g">Toggle power</button>` - toggle parameter 123 between 0 and 1, where 0 is the default value. After the toggle, does a full visualization refresh (because of `:g` option at the end).

* `edit-param:{{paramRef}}:{{options}}`

    Open the dialog window to edit and save the value of the parameter.

* `increment-param:{{paramRef}}:{{amount}}:{{options}}`

    Add `{{amount}}` to the value of the parameter `{{paramRef}}` and save the new value. Amount can be negative. Parameter is refreshed before being incremented and written to the device.

* `edit-visdata:{{key}}`

    Open the dialog window to edit and save the visualization string with key `{{key}}`. See [visualization strings table.](#visualization-strings-table)

`{{options}}` is a comma-separated list of flags. Currently supported flags:

* `g` - after the action, do a complete refresh of all visualization data. If ommitted, by default - only the affected parameters will be refreshed.

## Aliasing: `<alias>` tag

Additional tag can be used anywhere in the code:

```html
<alias name="{{aliasName}}" ... />
```

Alias tag should contain `name` attribute which specifies the name of the alias and one of the following attributes which specify the alias target:

* `network="{{networkRef}}"`
* `device="{{deviceRef}}"`
* `param="{{paramRef}}"`
* `vis="{{visualizationRef}}"`

Aliases are used when one object has to be referenced several times in the code.  It helps to make the references more understandable by giving them names. Also it allows to quickly change the target object, for example, if network controller was replaced and network MAC was changed.

Aliases can be used in references by specifying alias name after `~` (tilde symbol). Also, aliases can be used inside other aliases, e.g. network alias can be used inside the device alias.

Example code:

```html
<alias name="mainRoomNetwork" network="12-34-56-78-90-ab" />
<alias name="mainRoomEM482" device="~mainRoomNetwork>111" />
<div>Automation cycles:</div>
<div param-value="~mainRoomEM482>5000">-</div>
```

## Visualization strings table

Visualizations can store string values to Overvis database as key-value pairs. All stored values are loaded during the visualization start-up if `use-vis-data="yes"` specified in visualization settings tag.

To edit the value, use Overvis API. Also the value can be edited by the user though dialog. To open the dialog, add the `on-click="edit-visdata:{{key}}"` attribute to the button.

The following attribute allows you to use stored strings.

### Text replacement: `text-replacement="..."` attribute

Any container tag in HTML/SVG code has the additional possible attribute:

```
text-replacement="{{key}}"
```

It specifies, that the content of the tag should be replaced with the stored string with the key `{{key}}`.

Example:

```html
<svg use-vis-data="yes">
<text text-replacement="room-name">Main room</text>
<text on-click="edit-visdata:room-name">Edit</text>
</svg>
```

This example shows the room name as `"Main room"` by default, but also allows to edit it by clicking on the `"Edit"`. Edit will persist between page refreshes and different accounts accessing this visualization.
