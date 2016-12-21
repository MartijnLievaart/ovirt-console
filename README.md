ovirt-console
=============

I use [oVirt](http://www.ovirt.org/Home) at work to manage an internal virtual-machine cluster. Until recently, we were running oVirt 3.2.2, which is as of September 2014 two (almost three) releases out of date.

### VNC woes in oVirt 3.3 (and 3.4, 3.5)

A little while ago, I took the first step in getting us to the current version of oVirt, upgrading from 3.2.2 to 3.3.5 (and, later, to 3.4.4 then 3.5.0). The upgrade went well with one glaring exception: VNC consoles became way harder to launch. In 3.2.2, someone requesting a VNC console was shown a dialog box with a hostname and port number to which to connect, along with a password. It was a hassle to manually launch a VNC session, point it in the right direction, and enter the password—but it at least worked.

With 3.3.x through 3.5.x, there's good news and bad news. On the upside, the oVirt developers are aiming to automate the launching and authentication of the VNC session. When you request a VNC console in those versions of oVirt, your browser will download a file named `console.vv`. It's an INI-style file:

``` ini
[virt-viewer]
type=vnc
host=your.node.com
port=5903
password=ZOrRmRBNlzaK
delete-this-file=1
title=VNC
```

This setup mostly works for Linux users, assuming they have the `virt-viewer` application installed and configured to launch files given a `.vv` suffix.

### Poor Mac Users

For Mac users, however, the situation has gotten much worse. OS X has no clue what to do with that information. There's a [supposed fix](http://www.ovirt.org/SPICE_Remote-Viewer_on_OS_X), but so far I haven't gotten it to work.

In the interest of getting things running again in the short term, I've hacked up a small utility that aims to parse the information in the aforementioned `console.vv` file and do something useful with it. To use it,

* Clone this repository, put the `ovirt-console` script somewhere in your PATH, and make it executable.
* Log into oVirt and request a VNC console from a VM you manage.
* In a terminal window, run the script.

The script will assume that it's looking for `$HOME/Downloads/console.vv`. If that's the case, you can run it without arguments:

  `ovirt-console`

If your browser puts downloads elsewhere, or if the file gets renamed (*e.g.*, `console-2.vv`), then pass that filename instead:

  `ovirt-console /path/to/console-2.vv`

The `.vv` file used for your session will typically be deleted during its use. Use the `-k` option to keep it around.

### Troubleshooting

Since updating to oVirt Engine 3.5, I've noticed several occasions when the `console.vv` file arrived in mangled shape, with fields miscast (*e.g.*, the password in the host field). Each time, I simply requested a new console session; the resulting `console.vv` parsed correctly.

### Caveats

* The script originally worked only for VNC consoles. I don't use SPICE, but Mark Petersen has submitted a patch that passes a valid SPICE console request to the RemoteViewer application. I haven't tested this capability.
* The script is known to work under OS X 10.9.4, 10.9.5, 10.10, 10.10.1 and 10.10.2. No other operating systems or versions of OS X have been tested.
* I'm new to Python, so I've probably violated a gazillion best practices. You can patch or keep silent; whining is not allowed. :-)

