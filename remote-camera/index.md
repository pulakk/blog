# Accessing IP Cameras remotely

## Intro
Accessing your camera or any other device through the internet is a use case many people
ocassionally come across. But there are some challenges as well as pitfalls when it comes to
the choice of routers and the setup. In the next section I'll talk about what problems you 
might face, especially depending on your choice of router and ISP. Then, I am going to 
introduce my custom inventory and explain how I set up the camera and the router to be accessible
remotely. Do keep in mind that the tutorial will not be straightforward for someone who is 
new to the world of computers and networking. 

## Challenges
Picking the right camera is the first step. You might need features like
nightvision, communication through internet protocol ([IP](https://en.wikipedia.org/wiki/IP_address)), 
outdoor/indoor depending on your use case, microphone support, 
[ethernet cable (RJ45)](https://en.wikipedia.org/wiki/Ethernet)
or wireless connection,
etc. If you're going for an ethernet connected camera, you might need a 
[PoE](https://en.wikipedia.org/wiki/Power_over_Ethernet) device, to keep the camera
placement flexible. Try to pick a 802.3af compliant PoE if you're going for it.

You will need a [router](https://en.wikipedia.org/wiki/Router_(computing)) too. It will connect to the internet and 
receive traffic to and from your camera and the outside world, preferrably your home. 
Routers too come in variety of options and your choice of router will narrow down depending on whether your camera
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
it is highly unlikely you will get a public IP address, 
let alone a static one. That is especially the case
in LTE USB Dongle Modems. You'll be assigned a
[shared space private IP](https://en.wikipedia.org/wiki/IPv4_shared_address_space)
in the range `100.*.*.*` instead of a public IP Address.
DDNS will be of no help in this scenario. 

To solve this, you can setup a VPS server on a hosted service
and a VPN client on the router, in which case a VPN software
like [Wireguard](https://www.wireguard.com/) will come in handy. This is
because, although you might not have a public IP address, a hosted server
for e.g. in [Digital Ocean](https://www.digitalocean.com/), will. So your 
wireguard app can act as the client to connect to the server's public IP address
and create a tunnel. You would then be able to connect to the router
remotely as long as you can access the hosted server remotely.

This brings us to another problem. Not all routers have VPN client support. Many routers, 
like [ones from TP-Link](https://community.tp-link.com/en/home/forum/topic/168500)
, provide only VPN Server as an option.
So you can either carefully choose a router which supports VPN Clients, or flash a 
router with an alternative firmware like [OpenWRT](https://openwrt.org/) or 
[DDWRT](https://dd-wrt.com/support/router-database/). The open source alternatives
support most routers and have plenty of tutorials on how to install their firmware.

Flashing your router with a custom firmware means you might 
have to install drivers and configure the modem yourself.
This is clearly a hassle, given that the modem might be a USB dongle or an In-built
one. Dongles too might support multiple protocols ranging from 3G over
PPP/UMTS, QMI, NCM, RDNIS, CDC over ethernet and might provide the service through 3G or LTE.
This gives rise to a multitude of possibilies and you might end up in a sea of trial
and error for hours before figuring out the right configuration for your modem.

Accessing the camera remotely this point on, is just a matter of forwarding ports and
changing firewall rules.

I had faced the same problems on my end, starting with clueless guesses on 
the choice of the router and the camera
and spending hours figuring out solutions for all these problems. Hopefully this tutorial
should either straighten up the process for you or scare you away from the humongous difficulty of setting
up and accessing a single camera through the internet or both. Either way my purpose is served.

## Tutorial

This tutorial will guide you through the setup of a specific camera, and router model, so if you want to follow 
along my recommendation would be to replicate my custom setup. There is some leeway in the choice
of the camera but setting up the router to be accessible to the internet migh be different for
different routers.

* [My custom setup for accessing camera remotely](https://pulakk.github.io/blog/remote-camera/custom-setup)
