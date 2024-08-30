# See basic information
```
iw dev <iface> link
```

# Connect to AP without password
```
iw <iface> connect <AP SSID>
```

# Scan
```
iw <iface> scan
```

# Set interface in monitor mode
```
iw <iface> set monitor control
iw wlan0 interface add moniwlan0 type monitor
ip link set moniwlan0 up
```

```
iw dev <devname> set monitor <flag>*
        Set monitor flags. Valid flags are:
        none:     no special flags
        fcsfail:  show frames with FCS errors
        control:  show control frames
        otherbss: show frames from other BSSes
        cook:     use cooked mode
        active:   use active mode (ACK incoming unicast packets)
        mumimo-groupid <GROUP_ID>: use MUMIMO according to a group id
        mumimo-follow-mac <MAC_ADDRESS>: use MUMIMO according to a MAC address
```

https://miloserdov.org/?p=6227
https://sandilands.info/sgordon/teaching/its332y14s2/iw


# Regulatory info
```
iw reg get
```

```
iw reg set <country code>
```