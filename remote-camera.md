# Accessing IP Cameras remotely

Setting up a camera, to be viewable remotely in a place 
where there is no support for Wired ISPs, is a very challening task.

Picking the right camera is the first step. You might need features like
nightvision, communication through internet protocol ([IP](https://en.wikipedia.org/wiki/IP_address)), 
outdoor/indoor depending on your use case, microphone support, ethernet or wireless connection,
etc. If you're going for an ethernet connected camera, you might need a 
[PoE](https://en.wikipedia.org/wiki/Power_over_Ethernet) device, to keep the camera
placement flexible. Try to pick a 802.3af compliant PoE if you're going for it.

You will need a [router](https://en.wikipedia.org/wiki/Router_(computing)) too. It will connect to the internet and 
receive traffic to and from your camera and the outside world, preferrably your home. 
Routers too come in variety of options and your choice of router will narrow down depending on whether the camera
is wired or wireless. 

For connecting your router to the internet, you might choose to buy a separate [modem](https://en.wikipedia.org/wiki/Modem)
and connect it to the router through ethernet or wirelessly, or simply go with a Router
providing USB support for LTE Dongles or an in-built modem.

If the ISP ([Internet Service Provider](https://en.wikipedia.org/wiki/Internet_service_provider)) 
you're getting the internet access from, provides you with a static
public IP address, then most of your problems are solved. But your ISP is most probably
going to provide you with [dynamic IP addresses](https://www.techopedia.com/definition/28504/dynamic-internet-protocol-address-dynamic-ip-address),
in which case you might need to resort to a Dynamic DNS 
([DDNS](https://en.wikipedia.org/wiki/Dynamic_DNS)) service like [No-IP](https://www.noip.com/). 
Note: This also means that you will need to get a router with DDNS support.

But with ISPs moving towards [CGNAT](https://en.wikipedia.org/wiki/Carrier-grade_NAT), 
it will be hard to find an ISP which will give you a public IP address, 
let alone a static one. That is especially the case
in LTE USB Dongles for SIM cards. You'll be assigned a
shared space private IP in the range `100.*.*.*` instead of a public IP Address.
DDNS will be of no help in this scenario. 

To solve this, you can setup a VPS server on a hosted service
and a VPN client on the router, in which case a VPN software
like [Wireguard](https://www.wireguard.com/) will come in handy. This is
because, although you might not have a public IP address, a hosted server
for e.g. in [Digital Ocean](https://www.digitalocean.com/), will. So your 
wireguard app can act as the client can create a tunnel to the server
through the server's public ip address. You can then connect to the router
remotely as long as you can access the hosted server remotely.

This brings us to the another problem. Not all routers have VPN client support. 
So you can either carefully choose a router which provides that, or flash a 
router with an alternative firmware like [OpenWRT](https://openwrt.org/) or 
[DDWRT](https://dd-wrt.com/support/router-database/). The open source alternatives
support most routers and have plenty of tutorials on how to install their firmware.

Flashing your router with a custom firmware means you might 
have to install drivers and configure the modem yourself.
This is clearly a hassle, given that the modem might be a USB dongle or an In-built
one. Dongles too might support multiple protocols ranging from 3G over
PPP/UMTS, QMI, NCM, RDNIS, CDC over ethernet and might provide the service through 3G or LTE.
This gives rise to a multitude of possibilies and you might end up in a sea of trial
and errors for hours before figuring out the right configuration for your modem.

Accessing the camera remotely this point on, is just a matter of forwarding ports and
changing firewall rules.
