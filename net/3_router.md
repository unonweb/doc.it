# LINKS

- https://wells.ee/journal/2020-08-05-bypassing-att-fiber-modem-udmp/


# MODE
## bridge mode

- **disables routing mode**
- layer 3 > layer 2
- routing mode: analyze, handle, direct network traffic
- **stop functioning as a router**
- start functioning as a simple pass-through device **like a network switch**

Effectively, whatever comes in one side goes right out the other, just **like real-world traffic over a physical bridge** passes from one side of a river to the other.

In the bridge mode settings, you will need to input the new router's MAC address as the destination address. This tells your old router which new piece of network hardware is the endpoint for the bridged network traffic.
### issue: multiple routers in routing mode

- **double NAT**: traffic is passing through two routers, both in routing mode
- **WiFi-Transmission**: you're blasting twice the Wi-Fi transmissions into the same space creating a lot of radio noise in the processes
- if you reused the same Wi-Fi credentials, you'll have even more problems as now two different routers will be broadcasting the same SSID

Solution:
Put your old router into bridge mode so that it functions solely as a modem and hands off all the other network functions (like routing and Wi-Fi access) to your new hardware

## IP Passthrough

is a form of demilitarized zone (DMZ), wherein rather than bridging the connection, the ISP router simply puts your new router in a wide-open area with no firewall restrictions or NAT applied.

When your ISP-supplied router is put into IP Passthrough mode, it approximates a bridge mode but retains some routing functionality. This is almost always done because the ISP in question offers additional services beyond simple internet access, such as VoIP telephone service, internet-based TV services, and so on.
