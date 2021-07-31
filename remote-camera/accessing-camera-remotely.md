# Configuring OpenWRT router to enable access to camera feed
*You may want to go through the [Introduction](https://pulakk.github.io/blog/remote-camera/) 
page first, before proceeding with this tutorial.*

This tutorial describes how to configure your [OpenWRT](https://openwrt.org/)
router to make an IP camera accessible externally
from WAN. The camera used is a 
[HIKVision DS-2CD1321-I Dome camera](https://www.hikvision.com/ph/products/IP-Products/Network-Cameras/Value-Series/DS-2CD1321-I/)
connected to the router through a PoE adapter. 

### Connect the camera to the router
Use your camera's manual to setup the connection from the router to the camera. 
Connect your home laptop or PC to the router so that the camera and the laptop 
are on the same local network.

Once they are connected, you should be able to access the camera using it's
local IP address, for e.g. `192.168.1.64`, through the browser.

### Modify firewall policy in the OpenWRT Router

![Firewall Settings for zones in OpenWRT Router](https://raw.githubusercontent.com/pulakk/blog/main/remote-camera/images/open-firewall-zone-config.png)

Open the LuCI web interface of your OpenWRT router and go to `Network > Firewall > Zones`.

Add all the required interfaces to the WAN zone by going to `wan > Edit > Covered Networks`.
Set the *Allow forward to destination zones* to `lan`.

### Add port forwarding rules

![Firewall settings for Port forward in OpenWRT Router](https://raw.githubusercontent.com/pulakk/blog/main/remote-camera/images/openwrt-firewall-port-forward.png)

On the LuCI web Interface of your OpenWRT router, navigate to `Network > Firewall > Port forwards` 
and click the `add` button.

Set a name for the forwarding rule and add wan as the `Source zone`. Set an `External port` of your choice and 
set the internal port to the port through which the camera provides the feed. If the camera provides the feed
through the [RTSP Protocol](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol), the port will 
most likely be `554`. Set the `Internal IP Adress` to the IP address of the camera in the local network.

Click `save` and then `save and apply`.

### Wrapping up
You should be able to access your camera feed now by using a network video player for e.g. 
[VLC](https://www.videolan.org/vlc/).

If you have connected your router to a hosted server using a tunnel, you can access
the camera feed by using the IP address of the hosted server and the `External Port`
in the port forwarding rule set above.

Enjoy!

*To know more about setting up IP cameras and routers to be accessible remotely,
visit the [Introduction](https://pulakk.github.io/blog/remote-camera/) page.*
