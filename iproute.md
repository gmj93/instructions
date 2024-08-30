# VLANs
```
ip link add link eth0 name eth0.VLAN_ID type vlan id VLAN_ID
ip link set dev eth0.VLAN_ID up
ip addr add ... dev eth0.VLAN_ID
```

# Manipulating bridges

## Create
```
ip link add name br1 type bridge
ip link set dev br1 up
ip link set dev eth0 master br1
ip link set dev eth1 master br1
```

## Remove an interface
```
ip link set dev eth0 nomaster
```

## Remove bridge
```
ip link del br0
```

## Show MACs
```
bridge -d fdb show (dev <bridge>)
```

# VLAN manipulation

To show if there is any vlan ingress/egress filters:
```
bridge vlan show
```

To add rules to a given interface:
```
bridge vlan add dev eth1 <vid, pvid, untagged, self, master>
```

To remove rules. Use the same parameters as `vlan add` at the end of the command to delete a specific rule.
```
bridge vlan delete dev eth1
```

## IPv6
```
ip -6 addr add <ipv6> dev <interface>
```

## Route management
```
ip route add {NETWORK/MASK} via {GATEWAYIP}
ip route add {NETWORK/MASK} dev {DEVICE}
ip route add default {NETWORK/MASK} dev {DEVICE}
ip route add default {NETWORK/MASK} via {GATEWAYIP}
```

# References
https://unix.stackexchange.com/questions/255484/how-can-i-bridge-two-interfaces-with-ip-iproute2
https://wiki.archlinux.org/index.php/Network_bridge
