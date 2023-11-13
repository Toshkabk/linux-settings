# Настраиваем vESR





## DHCP

```text
ip dhcp-server
ip dhcp-server pool vlan100
network xxx
address-range xxx-xxx
default-router xxx
domain-name xxx
end
commit
confirm
save
```



## Логи

```text
configure
syslog host SRV1
remote-address xxx
transport udp
severity debug
port 514
end
commit
confirm
save
```

## NAT

```text
configure
object-group network LOCAL
ip prefix xxx
exit
object-group network WAN
ip address-range xxx
exit
nat source
pool WAN
ip address-range xxx
ruleset MASQUERADE
to interface g 1/0/1
rule 1
match source-address LOCAL
action source-nat pool WAN
enable
end
commit
confirm
save
```

## IPSEC

Настройки айписека должны быть абсолютно одинаковые на обоих роутерах

```text

object-group service ISAKMP
 port-range 500
 exit

security ike proposal ike_prop1
 dh-group 2
 authentication algorithm md5
 encryption algorithm aes128
 exit

security ike policy ike_pol1
 pre-shared-key hexadecimal 123FFF
 proposal ike_prop1
 exit

security ike gateway ike_gw1
 ike-policy ike_pol1
 mode route-based
 bind-interface vti 1
 version v2-only
 exit

security ipsec proposal ipsec_prop1
 authentication algorithm md5
 encryption algorithm aes128
 exit

security ipsec policy ipsec_pol1
 proposal ipsec_prop1
 exit

security ipsec vpn ipsec1
 ike establish-tunnel immediate
 ike gateway ike_gw1
 ike ipsec-policy ipsec_pol1
 enable
 exit
exit

commit
confirm
```

## OSPF

```text

# R1:
router ospf log-adjacency-changes
router ospf 1
 area 1.1.1.1
  network xxx
  network xxx
  network xxx
  enable
  exit
 enable
 exit

# R2
router ospf log-adjacency-changes
router ospf 1
 area 1.1.1.1
  network xxx
  network xxx
  network xxx
  enable
  exit
 enable
 exit

# R1
tunnel vti 1
 ip firewall disable
 local address xxx
 remote address xxx
 ip address xxx
 ip ospf instance 1
 ip ospf area 0.0.0.0
 ip ospf
 enable
 exit

#RTR2
tunnel vti 1
 ip firewall disable
 local address xxx
 remote address xxx
 ip address xxx
 ip ospf instance 1
 ip ospf area 1.1.1.1
 ip ospf
 enable
 exit
```
