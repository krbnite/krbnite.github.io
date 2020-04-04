

MANet: Mobile Adhoc Network


Why is networking needed?  Easy: to access data or compute not available in a single device.  

For example, imagine all cars were networked: they could plan efficient routes as a holistic organism!

More common example: connecting to Netflix for access to nigh-unlimited media content.

Client-Server Model
* single server, one-to-many clients
* server manages a resource, responds to client requests
```
 ________      request         ________                      __________
|        | -----------------> |        |   handle request   |          |
| CLIENT |                    | SERVER | <----------------> | RESOURCE |
|________| <----------------- |________|                    |__________|
               response
```

LAN: Local Area Network.  Typically spans a building or campus (relatively small network).  You 
hook in through an ethernet cable (or WiFi). 

WAN: Wide Area Network. This is a huge network which you wouldn't call "localized."  For example,
the internet is a WAN.  Basically, it's a network of many LANs. 

MANET:  Mobile Ad Hoc Network.  Continually changing, typicall short-range network composed of wireless, mobile 
devices.  Most common for IoT devices.  (Example: you walk through a MANET area, your cell connects, network
reconfigures to accept connection, you walk out of area, cell disconnects, etc.)
