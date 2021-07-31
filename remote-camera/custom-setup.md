# My custom setup for accessing an IP Camera Remotely

## Inventory

* Router: [TP-Link MR3020 v3.20](https://www.tp-link.com/in/home-networking/3g-4g-router/tl-mr3020/#overview) 
 (USB for 3G/4G Dongle + Ethernet + WiFi) - Note: Make sure the router version is not below 3.0
* USB Dongle: [Huawei E3372h-607](https://consumer.huawei.com/in/support/routers/e3372h-607/) "Airtel 4G", SIM: Airtel
* Camera: [HIKVision DS-2CD1321-I](https://cdn.cnetcontent.com/24/87/24873b9f-7f11-406c-ae4d-608b2de0c08e.pdf) 2.0MP NightVision Dome
* PoE: [TP-LINK TL-PoE150S](https://www.tp-link.com/us/business-networking/accessory/tl-poe150s/)

Choosing this setup meant that the internet access from my router would be behind a CGNAT. So, DDNS did not work for me
and I had to create a wireguard VPN tunnel from my router to a hosted server to access my router
through that server instead. I
picked Digital ocean but any other cloud provider should do. So here's what I did for 
accessing the camera behind the router remotely.

## Overview
* OpenWRT firmware on the router
* USB Dongle Protocol: NCM; Service: LTE
* Wireguard Client: OpenWRT opkg installation; configured through LuCI
* Wireguard VPN Server on Digital Ocean droplet
* Custom domain name pointing to Digital Ocean droplet (Optional)


## Steps

* [Connect your router to the internet](https://pulakk.github.io/blog/remote-camera/connect-router-to-internet)
# [Accessing your camera](https://pulakk.github.io/blog/remote-camera/accessing-camera)
