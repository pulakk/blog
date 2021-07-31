# Accessing TP-Link MR3020 router through the internet

*If you're new to this tutorial, you may like to go through the [Introduction](https://pulakk.github.io/blog/remote-camera/)
to know if you actually need these steps and if so, to understand why the setup is tedious and time consuming.*

My router was connected to the internet behind a [CGNAT](https://en.wikipedia.org/wiki/Carrier-grade_NAT). This meant that
I would require VPN client support on my router to access it through a hoster server.

```
[Home Laptop] ----> Hosted Server <==== +
  |                 <public ip>        ||
  |                     |              ||
  |  cannot access      |              ||
 [x] router through     |              ||
  |  CGN Gateway        |              || VPN
  |                     |              || Tunnel
  + - - - - - - -    CGN gateway       ||
                    <public ip>        ||
                        |              ||
                        |              ||
                     Router       <==== +
               <shared space private ip>
                        |
                     Camera
                   <private ip>
```

[TP-Link routers do not support VPN Clients](https://community.tp-link.com/en/home/forum/topic/168500) at the time of writing this tutorial. Hence, I started by flashing my TP-Link router with the [OpenWRT](https://openwrt.org/)
firmware so that I could manually install a VPN client on the router.
After flashing the router, the USB Modem needed some drivers to be installed prior to configuration. Once the router was connected to the internet, I had to choose a VPN software and I picked
[Wireguard](https://www.wireguard.com/). VPN setup is a two step process, (i) setting up Wireguard server on a hosted server (e.g. [Digital Ocean](https://www.digitalocean.com/)) and (ii) installing and configuring Wireguard client on the router. Each of these steps are described 
below in great detail.

### Flashing router with OpenWRT firmware
Read through the official [tutorial for flashing OpenWRT firmware to an MR3020 V3 router](https://openwrt.org/toh/tp-link/tl-mr3020_v3). Skip the `Install OpenWRT (Generic Explanation)` link (trying to install the firmware from your MR3020 v3 router's web interface won't work as it does not accept flash firmware from external sources) and go straight to the `Configure a TFTP server as follows:` portion. There are numerous tutorials on youtube as well which show you how to install OpenWRT firmware on an MR3020 router via TFTP. The process is described briefly below -

* Make sure you have a PC with an Ethernet port to connect to the router. The wireless interface will be disabled after the flashing process and you will not be able to connect to your router wirelessly after booting.
* Download and install `Tftpd32-4.60-setup.exe` from [Pulpstone](https://pulpstone.pw/tools/) if you have a windows PC. If you don't then look online for links for your Operating System. [TFTP](https://en.wikipedia.org/wiki/Trivial_File_Transfer_Protocol) is the protocol through which the OpenWRT firmware will be transferred to your router for the flashing process.
* The `Firmware OpenWrt Install` column in the `Installation` table of the Official OpenWRT tutorial for MR3020 V3 mentioned above, contains the download link for the recovery bin file. Download that file and save it to the folder where tftpd32 was installed and rename it to tp_recovery.bin 
* Set your ethernet IP to 192.168.0.225, be careful not to use 192.168.0.255 instead.
* Shutdown your router 
* Restart the router while holding down the reset button which is present on top. Your router LEDs will blink, indicating that it's in reset mode.
* Start the tftpd32 process immediately. 
* In the tftpd UI, within 4-5 seconds you'll see the tp_recovery.bin file being transferred. If its not being transferred, make sure that the tftpd server and your ethernet IP address are setup correctly.
* If the transfer is successful, after some time your router will stop blinking and will be ready for configuration.
* Change your ethernet IP setup back to DHCP and open 192.168.1.1 on your browser. Login via user `root` and password `openwrt`. 

You've successfully flashed your router with the OpenWRT Firmware.

### Setting up Huawei E3372h-607 USB Modem

The [official OpenWRT tutorial for setting up WWAN connection through 3G/4G/LTE](https://openwrt.org/docs/guide-user/network/wan/wwan/start) has many options you can try.

You may try using the one of the following tutorials present there: 
* [3G / UMTS](https://openwrt.org/docs/guide-user/network/wan/wwan/3gdongle)
* [CDC Ethernet](https://openwrt.org/docs/guide-user/network/wan/wwan/ethernetoverusb_cdc)
* [LTE / QMI](https://openwrt.org/docs/guide-user/network/wan/wwan/ltedongle)
* [NCM](https://openwrt.org/docs/guide-user/network/wan/wwan/ethernetoverusb_ncm)
* [RNDIS](https://openwrt.org/docs/guide-user/network/wan/wwan/ethernetoverusb_rndis)

Which tutorial will work for you depends on what protocol your 
Modem supports (UMTS, NCM, RNDIS, CDC Ethernet) for connecting to your 
router and whether it provides the internet service through 3G or 4G/LTE. 

If you want to manually check the supported protocol in your modem, you can send AT commands to communicate
with the Dongle. This is not a mandatory step as you can simply try the tutorials mentioned
above and see which one works, but it might help you get a direction on which one to try first.
The OpenWRT tutorial on sending [AT Commands](https://openwrt.org/docs/guide-user/network/wan/wwan/at_commands) to 
your router can help you get started. On the OpenWRT terminal, you can use the `lsusb` command
and the `dmesg` command (kernel logs) to see if your USB Dongle is able to connect
properly to your router. After it is connected, you may send AT commands using `socat - /dev/ttyUSB<number>`.

My E3372h-607 modem did not support `AT^PORTMODE?` command so I was not able to check which
protocol would work. Hence, I went ahead and tried the tutorials one by one.

The 3G/UMTS, CDC Ethernet and the RNDIS tutorials did not work for me. But finally the NCM tutorial 
worked out well and I could see the following kernel logs which indicated that the driver was
able to register the usb device.
```
huawei_cdc_ncm 1-1:1.2: cdc-wdm0: USB WDM device
huawei_cdc_ncm 1-1:1.2 wwan0: register 'huawei_cdc_ncm' at usb-101c0000.ehci-1, Huawei CDC NCM device, ...
```
The system logs also showed that the Dongle was able to obtain a DHCP lease for internet from the ISP.
```
daemon.notice netifd: E3372_Dongle (1733): sending -> AT
daemon.notice netifd: E3372_Dongle (1733): sending -> ATZ
daemon.notice netifd: E3372_Dongle (1733): sending -> ATQ0
daemon.notice netifd: E3372_Dongle (1733): sending -> ATV1
daemon.notice netifd: E3372_Dongle (1733): sending -> ATE1
daemon.notice netifd: E3372_Dongle (1733): sending -> ATS0=0
daemon.notice netifd: E3372_Dongle (1733): sending -> AT+CGDCONT=1,"IP","airtelgprs.com"
daemon.notice netifd: E3372_Dongle (1733): Configuring modem
daemon.notice netifd: E3372_Dongle (1733): Starting network E3372_Dongle
daemon.notice netifd: E3372_Dongle (1733): Connecting modem
daemon.notice netifd: E3372_Dongle (1733): sending -> AT^NDISDUP=1,1,"airtelgprs.com"
daemon.notice netifd: E3372_Dongle (1733): Setting up wwan0
daemon.notice netifd: Interface 'E3372_Dongle' is now up
daemon.notice netifd: Network device 'wwan0' link is up
daemon.notice netifd: Network alias 'wwan0' link is up
daemon.notice netifd: Interface 'E3372_Dongle_4' is enabled
daemon.notice netifd: Interface 'E3372_Dongle_4' has link connectivity
daemon.notice netifd: Interface 'E3372_Dongle_4' is setting up now
user.notice firewall: Reloading firewall due to ifup of E3372_Dongle (wwan0)
daemon.notice netifd: E3372_Dongle_4 (2114): udhcpc: started, v1.30.1
daemon.notice netifd: E3372_Dongle_4 (2114): udhcpc: sending discover
daemon.notice netifd: E3372_Dongle_4 (2114): udhcpc: sending discover
daemon.notice netifd: E3372_Dongle_4 (2114): udhcpc: sending select for <some-shared-space-ip>
daemon.notice netifd: E3372_Dongle_4 (2114): udhcpc: lease of <some-shared-space-ip> obtained, lease time <duration>
daemon.notice netifd: Interface 'E3372_Dongle_4' is now up
```

You'll see similar kernel and system logs for other protocols as well.

Note: Try to use the Router's web UI to
create the Interface for your USB Modem. Fields like the `Protocol`, `Modem Device`, and `Service Type`
will let you know whether the USB has connected to the router properly through the 
drivers that you installed. If it is connected, you'll see fields like `/dev/tty/USB0`,
`/dev/cdc-wm0` etc. being listed on the `Modem Device` field, and `NCM`, `QMI` etc. being
listed on the `Protocol` field when you try to create the interface. If the device is not configured
properly you will not see these options on the UI. This is a better feedback on whether your 
setup is working properly, rather than manually adding an interface to `/etc/config/network` as indicated
in the the [Network configuration](https://openwrt.org/docs/guide-user/network/wan/wwan/ethernetoverusb_ncm#network_configuration)
sections, where you have to depend entirely on kernel and system logs to know 
whether your device is connected properly.

### Creating WireGuard VPN Server through on Digital Ocean
Sign up / Log in to Digital ocean and create a droplet. You can use the basic ubuntu droplet 
which will cost you close to `$5` per month.
Log into the machine and follow the steps mentioned below.

[This tutorial on setting up a VPS](https://reposhub.com/python/security/mochman-Bypass_CGNAT.html) was
used as a reference while setting up wireguard server and client.
We will not follow the tutorial completely because we'll be using the already available OpenWRT 
router Web interface UI (LuCI) for setting up the VPN client. The VPN Server setup is same as the one provided in the
tutorial link above, so you may refer to the `VPS Setup` section through that tutorial directly or follow along. 

#### (a) System config
Uncomment the `net.ipv4.ip_forward=1` line in the system config file using
```
nano /etc/sysctl.conf
```
In case the line is not present, add it and then save the file. run
```
sysctl -p
```
You should see the `net.ipv4.ip_forward=1` line printed on the console.

#### (b) Install wireguard
```
apt update && apt upgrade && apt install wireguard
```
#### (c) Generate private and public keys for the Server
```
wg genkey | tee -a /etc/wireguard/privatekey | wg pubkey | tee /etc/wireguard/publickey
```
You can view the keys using 
```
cat /etc/wireguard/privatekey
cat /etc/wireguard/publickey
```
You'll need this public key when we create the wireguard client on the router.

#### (d) Create config for wireguard interface
Create the wireguard interface config file and open it.

```
mkdir /etc/wireguard/ && touch /etc/wireguard/wg0.conf
nano /etc/wireguard/wg0.conf
```

Paste the following config to the file opened above.

```
[Interface]
PrivateKey = <PRIVATE-KEY-OF-SERVER>
ListenPort = 51820
Address = 10.0.0.1/24

PostUp = iptables -t nat -A PREROUTING -p tcp -i eth0 '!' --dport 22 -j DNAT --to-destination 10.0.0.2; iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source <DIGITAL-OCEAN-DROPLET-IP-ADDRESS>
PostUp = iptables -t nat -A PREROUTING -p udp -i eth0 '!' --dport 51820 -j DNAT --to-destination 10.0.0.2;

PostDown = iptables -t nat -D PREROUTING -p tcp -i eth0 '!' --dport 22 -j DNAT --to-destination 10.0.0.2; iptables -t nat -D POSTROUTING -o eth0 -j SNAT --to-source  <DIGITAL-OCEAN-DROPLET-IP-ADDRESS>
PostDown = iptables -t nat -D PREROUTING -p udp -i eth0 '!' --dport 51820 -j DNAT --to-destination 10.0.0.2;

[Peer]
PublicKey = <PUBLIC-KEY-OF-ROUTER>
AllowedIPs = 10.0.0.2/32
```

Replace `<PRIVATE-KEY-OF-SERVER>` with the private key that was generated earlier and `<DIGITAL-OCEAN-DROPLET-IP-ADDRESS>`
with the IP address of the Digital ocean droplet that you created and are logged into. You may need to change
`eth0` with the interface through which your droplet accesses the internet. The `<PUBLIC-KEY-OF-ROUTER>` will
be created later when we generate keys for the wireguard client in the router.

#### (e) Start the wireguard server
Since the `<PUBLIC-KEY-OF-ROUTER>` is not generated yet, this step should be done after the client is setup.
Once the keys are generated in the router (described later), you can run this command.
```
systemctl start wg-quick@wg0
```

### Setting up Wireguard VPN Client on the router

#### (a) Installing wireguard on router
Connect to the router through `ssh` or `PuTTY` and install wireguard using
```
opkg update
opkg install wireguard luci-proto-wireguard
reboot
```

#### (b) Generate private and public keys for the router
```
wg genkey | tee privatekey | wg pubkey > publickey
```
You can view the keys using 
```
cat privatekey
cat publickey
```
Replace `<PUBLIC-KEY-OF-ROUTER>` in the Digital ocean wireguard server config with this public key. You can now
start the wireguard server using the `systemctl` command described above.

#### (c) Configure WireGuard client
Connect to the router's web interface from the browser at `192.168.1.1`
* Log in and go to `Network > Interfaces > Add New Interface`
* Set the protocol as `Wireguard VPN` (if the installation of `luci-proto-wireguard` 
did not complete successfully then the `Wireguard VPN` protocol will not be found
in the list)
* Give it any name like WG0 and click on `Create Interface`
* Enter the private key of the router that was generated earlier.
* Add `10.0.0.2/24` to IP Addresses field
and click the `+` button
* Go to `firewall` settings tab and set `Create / Assign Firewall zone` to `wan`
* Go to `peer` tab, and paste the pulic key of the Digital Ocean server.
`Preshared key` will remain empty. Set the allowed IPs to `0.0.0.0/0` and the `Endpoint host` to the Public IP Address
of the Digital Ocean droplet. Check the box on `Router allowed IPS`. You may set the persistent keep alive to `25`.
* Click `save`

The wireguard client and server are setup and you should be able to test the connection on the server using
```
ping 10.0.0.2
```
or 
```
wg show
```
You can test the wireguard client on the router by going to `Status > Wireguard Status` on the
router's UI.
