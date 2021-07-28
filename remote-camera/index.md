# Accessing IP Cameras remotely

## Intro
Setting up a camera, to be viewable remotely is a very challening task.

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

This brings us to another problem. Not all routers have VPN client support. 
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

I had faced the same problems on my end, starting with clueless guesses on 
the choice of the router and the camera
and spending hours figuring out solutions for all these problems. Hopefully this tutorial
should either straighten up the process for you or scare you away from the humongous difficulty of setting
up and accessing a single camera through the internet.

## My custom setup

Inventory -
* Router: [TP-Link MR3020 v3.20](https://www.tp-link.com/in/home-networking/3g-4g-router/tl-mr3020/#overview) 
 (USB for 3G/4G Dongle + Ethernet + WiFi) - Note: Make sure the router version is not below 3.0
* USB Dongle: [Huawei E3372h-607](https://consumer.huawei.com/in/support/routers/e3372h-607/) "Airtel 4G", SIM: Airtel
* Camera: [HIKVision DS-2CD1321-I](https://cdn.cnetcontent.com/24/87/24873b9f-7f11-406c-ae4d-608b2de0c08e.pdf) 2.0MP NightVision Dome
* PoE: [TP-LINK TL-PoE150S](https://www.tp-link.com/us/business-networking/accessory/tl-poe150s/)

Software setup -
* OpenWRT firmware on the router
* USB Dongle Protocol: NCM; Service: LTE
* Wireguard Client: OpenWRT opkg installation; configured through LuCI
* Wireguard VPN Server on Digital Ocean droplet
* Custom domain name pointing to Digital Ocean droplet (Optional)

## Steps
### Flashing router with OpenWRT firmware
Read through the official [tutorial for flashing OpenWRT firmware to an MR3020 V3 router](https://openwrt.org/toh/tp-link/tl-mr3020_v3). Skip the `Install OpenWRT (Generic Explanation)` link and go straight to the `Configure a TFTP server as follows:` portion. There are numerous tutorials on youtube as well which show you how to install OpenWRT firmware on an MR3020 router via TFTP. The process is described briefly below -

* Make sure you have a PC with an Ethernet port to connect to the router. The wireless interface will be disabled after the flashing process and you will not be able to connect to your router wirelessly after booting.
* Download and install `Tftpd32-4.60-setup.exe` from [Pulpstone](https://pulpstone.pw/tools/) if you have a windows PC. If you don't then look online for links for your Operating System. [TFTP](https://en.wikipedia.org/wiki/Trivial_File_Transfer_Protocol) is the protocol through which the OpenWRT firmware will be transferred to your router for the flashing process.
* The `Firmware OpenWrt Install` column in the `Installation` table contains the download link for the recovery bin file. Download that file and save it to the folder where tftpd32 was installed and rename it to tp_recovery.bin 
* Set your ethernet IP to 192.168.0.225, be careful not to use 192.168.0.255 instead.
* Shutdown your router 
* Start the router while holding down the reset button present on it. Your router will leds will blink, indicating that it's in reset mode.
* Start the tftpd32 process immediately. 
* In the tftpd UI, within 4-5 seconds you'll see the tp_recovery.bin file being transferred. If its not being transferred, recheck the tftpd server and your ethernet IP address are setup correctly.
* If the transfer is successful, after some time your router will stop blinking and will be ready for configuration.
* Change your ethernet IP setup back to DHCP and open 192.168.1.1 on your browser. Login via user `root` and password `openwrt`. 

You've successfully flashed your router with the OpenWRT Firmware.
