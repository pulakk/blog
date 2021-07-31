# Configuring router to enable access to camera feed
*Go through the [Introduction](https://pulakk.github.io/blog/remote-camera/) page if you want to
access an IP Camera's feed remotely.*

This tutorial describes how to configure your router to make an IP camera accessible externally
from WAN. The camera used is a HIKVision DS-2CD1321-I Dome camera connected to the router
through a PoE adapter. 

### Connect the camera to the router
Use your camera's manual to setup the connection from the router to the camera. 
Connect your home laptop or PC to the router so that the camera and the laptop 
are on the same local network.

Once they are connected, you should be able to access the camera using it's
local IP address, for e.g. `192.168.1.64`, through the browser.

### Modify firewall policy in the OpenWRT Router
![Firewall Settings for OpenWRT Router](https://raw.githubusercontent.com/pulakk/blog/main/remote-camera/images/open-firewall-zone-config.png)
Open the LuCI web interface of your OpenWRT router and go to `Network > Firewall > Zones`.
Add all the required interfaces like  to the WAN zone by going to `wan > Edit > Covered Networks`.
Set the *Allow forward to destination zones* to `lan`.

### Add port forwarding rules
