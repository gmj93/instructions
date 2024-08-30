# Clear all rules

## Accept all traffic first to avoid ssh lockdown via iptables firewall rules
```
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```

## Flush All Iptables Chains/Firewall rules
```
iptables -F
```

## Delete all Iptables Chains
```
iptables -X
```

## Flush all counters too
```
iptables -Z 
```

## Flush and delete all nat and mangle rules for IPv4 and IPv6
```
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -t raw -F
iptables -t raw -X 
```

```
ip6tables -P INPUT ACCEPT && \
ip6tables -P FORWARD ACCEPT && \
ip6tables -P OUTPUT ACCEPT && \
ip6tables -F && \
ip6tables -X && \
ip6tables -Z  && \
ip6tables -t nat -F && \
ip6tables -t nat -X && \
ip6tables -t mangle -F && \
ip6tables -t mangle -X && \
ip6tables -t raw -F && \
ip6tables -t raw -X 
```

```
iptables -P INPUT ACCEPT && \
iptables -P FORWARD ACCEPT && \
iptables -P OUTPUT ACCEPT && \
iptables -F && \
iptables -X && \
iptables -Z  && \
iptables -t nat -F && \
iptables -t nat -X && \
iptables -t mangle -F && \
iptables -t mangle -X && \
iptables -t raw -F && \
iptables -t raw -X 
```

# Port forwarding

* Public interface: eth1
* Public port: 80
* Internal interface: eth0
* Gateway (running iptables) address: 10.0.0.1
* Internal node (running server) address: 10.0.0.20

```
iptables -A FORWARD -i eth1 -o eth0 -p tcp --syn --dport 80 -m conntrack --ctstate NEW -j ACCEPT

iptables -A FORWARD -i eth1 -o eth0 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i eth0 -o eth1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -P FORWARD DROP

iptables -t nat -A PREROUTING -i eth1 -p tcp --dport 80 -j DNAT --to-destination 10.0.0.20
iptables -t nat -A POSTROUTING -o eth0 -p tcp --dport 80 -d 10.0.0.20 -j SNAT --to-source 10.0.0.1
```
