# Accessing IP Cameras remotely

## Introduction
Accessing your camera or any other device through the internet is a use case many people
ocassionally come across. But there are some challenges as well as pitfalls when it comes to
the choice of routers and the setup. In the next section I'll talk about what problems you 
might face, especially depending on your choice of router and ISP. Then, I am going to 
introduce the inventory and explain how I set up the camera and the router to be accessible
remotely. 

Do keep in mind that the tutorial will not be straightforward for someone who is 
new to the world of computers and networking. 

## Challenges

### Camera
Picking the right camera is the first step. You might need features like
nightvision, communication through internet protocol ([IP](https://en.wikipedia.org/wiki/IP_address)), 
outdoor/indoor depending on your use case, microphone support, 
[ethernet cable (RJ45)](https://en.wikipedia.org/wiki/Ethernet)
or wireless connection,
etc. If you're going for an ethernet connected camera, you might need a 
[PoE](https://en.wikipedia.org/wiki/Power_over_Ethernet) device, to keep the camera
placement flexible. Try to pick a 802.3af compliant PoE if you're going for it.

### Router and Modem
You will need a [router](https://en.wikipedia.org/wiki/Router_(computing)) too. It will connect to the internet and 
receive traffic to and from your camera and the outside world, preferrably your home. 
Routers too come in a variety of options and your choice of router will narrow down depending on whether your camera
is wired or wireless. 

For connecting your router to the internet, you might choose to buy a separate [modem](https://en.wikipedia.org/wiki/Modem)
and connect it to the router through ethernet or wirelessly, or simply go with a Router
providing USB support for LTE Dongles or an in-built modem.

### ISP
The IP address assigned to you by your ISP ([Internet Service Provider](https://en.wikipedia.org/wiki/Internet_service_provider)) , i.e. the [WAN](https://en.wikipedia.org/wiki/Wide_area_network) IP, is not always a public IP Address. 
Usually in the modern world, where IPv4 addresses running out, your ISP will put you behind a 
[Carrier grade NAT](https://en.wikipedia.org/wiki/Carrier-grade_NAT). Your setup will vary according
to what kind of IP Address is assigned to you.

```
IP Address assigned to you by ISP
 |
 + ---- [static public IP]
 |          |
 |   Access router directly 
 |     through public IP
 |
 + ---- [dynamic public IP] 
 |          |
 |     Setup Dynamic DNS on router
 |  (Pick Dynamic DNS supported router)
 |
 + ---- [(shared space) private IP (behind CGNAT)]
           |
        VPN Client on router
    Access router through hosted VPN server
```

#### (a) Static public IP Addresses
If the ISP
you're getting the internet access from, provides you with a static
public IP address, then most of your problems are solved herein. Any
choice of router and camera should work for you, because you can access your
router, and in effect, your camera, by making requests to that static
public IP address.

#### (b) Dynamic public IP Addresses
Your ISP is may provide you with 
[dynamic public IP addresses](https://www.techopedia.com/definition/28504/dynamic-internet-protocol-address-dynamic-ip-address),
in which case you can make use of a Dynamic DNS 
([DDNS](https://en.wikipedia.org/wiki/Dynamic_DNS)) service like [No-IP](https://www.noip.com/). 
Note: This also means that you will need to get a router with DDNS Client support.

Dynamic IP addresses simply mean that your IP Address may change over time. Without keeping track of the
router's latest IP Address, you can not connect to it. Hence, DDNS
support is needed.

#### (c) Private IP Addresses
This is the most common scenario.

With ISPs moving towards [CGNAT](https://en.wikipedia.org/wiki/Carrier-grade_NAT), 
it is highly unlikely you will get a public IP address, 
let alone a static one. That is especially the case
in LTE USB Dongle Modems. You'll be assigned a
[shared space private IP](https://en.wikipedia.org/wiki/IPv4_shared_address_space)
in the range `100.*.*.*` instead of a public IP Address. In simple words, the ISP will
make requests to the internet on behalf of you, essentially hiding you behind
their network and so other servers on the internet can only talk to your ISP,
but not directly to you.
DDNS will be of no help in this scenario. 

To solve this, you can setup a VPS server on a hosted service
and a VPN client on the router, in which case a VPN software
like [Wireguard](https://www.wireguard.com/) will come in handy. This is
because, although you might not have a public IP address, a hosted server
for e.g. in [Digital Ocean](https://www.digitalocean.com/), will. So your 
wireguard app can act as the client to connect to the server's public IP address
and create a tunnel. You would then be able to connect to the router
remotely as long as you can access the hosted server.

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
PPP/UMTS, QMI, NCM, RDNIS, CDC over ethernet and might provide the service through 3G/4G/LTE.
This gives rise to a multitude of possibilies and you might end up in a sea of trial
and error for hours before figuring out the right configuration for your modem.

### Conclusion
If you are able to access your router through the internet, connecting to your camera
is just a matter of forwarding ports and
changing firewall rules in the router.

I had faced the same problems on my end, starting with clueless guesses on 
the choice of the router and the camera
and spending hours figuring out solutions for all these problems. Hopefully this tutorial
should either straighten up the process for you or scare you away from the humongous difficulty of setting
up and accessing a single camera through the internet or both. Either way my purpose is served.

## Tutorial

This tutorial will guide you through the setup of a specific camera, and router model, so if you want to follow 
along, my recommendation would be to replicate the same setup. There is some leeway in the choice
of the camera but the process of setting up the router to be accessible through the internet might be different for
different routers.

* [My custom setup for accessing camera remotely](https://pulakk.github.io/blog/remote-camera/custom-setup)
