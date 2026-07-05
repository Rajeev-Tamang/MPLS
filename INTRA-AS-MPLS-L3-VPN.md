![INTRA-AS-MPLS-L3-VPN](INTRA%20AS%20MPLS%20L3%20VPN.jpg)
# INTRA-AS-MPLS-L3-VPN
# R1
```
    >  vrf definition nepal-bank
    >
    > rd 100:1
    > 
    > address-family ipv4
    >
    > route-target both 1:1
    >
    > 
    > vrf definition laxmi-sunrise-bank
    >
    > rd 100:2
    > 
    > address-family ipv4
    >
    > route-target both 2:2
    > 
    > int f 1/0
    > 
    > vrf forwarding nepal-bank
    > 
    > ip add 192.168.15.1 255.255.255.0
    > 
    > no shut
    > 
    > exit
    >
    > int f 2/0
    > 
    > vrf forwarding laxmi-sunrise-bank
    > 
    > ip address 192.168.16.1 255.255.255.0
    > 
    > no shutdwon
    > 
    > exit
    > 
    > int f 0/0
    > 
    > ip address 192.168.12.1 255.255.255.0
    > 
    > mpls ip
    > 
    > no shutdown
    > 
    > exit
    >
    > int loop 0
    > 
    > ip address 1.1.1.1 255.255.255.255
    > 
    > exit
    >
    > ip cef
    > 
    > mpls ip
    >
    > mpls ldp router-id loopback 0
    >
    > router ospf 2 vrf nepal-bank
    > 
    > network 192.168.15.0 0.0.0.255 area 0
    >
    > redistribute bgp 100 subnets
    > 
    >router ospf 1
    > 
    >network 1.1.1.1 0.0.0.0 area 0
    > 
    >network 192.168.12.0 0.0.0.255 area 0
    > 
    >router rip
    > 
    >version 2
    > 
    >no auto-summary
    >
    >address-family ipv4 vrf laxmi-sunrise-bank
    >
    > network 192.168.16.0
    >
    > redistribute bgp metric 1
    > 
    > no auto-summary
    >
      > version 2
    >
    > exit-address-family
    >
    > router bgp 100
    >
    > bgp log-neighbor-changes
    > 
    > network 1.1.1.1 mask 255.255.255.255
    > 
    > neighbor 4.4.4.4 remote-as 100
    > 
    > neighbor 4.4.4.4 update-source Loopback0
    > 
    > neighbor 4.4.4.4 next-hop-self
    >
    > address-family vpnv4
    > 
    > neighbor 4.4.4.4 activate
    > 
    > neighbor 4.4.4.4 send-community extended
    > 
    > exit-address-family
    > 
    > address-family ipv4 vrf laxmi-sunrise-bank
    > 
    > redistribute rip
    
    > exit-address-family
    >
    > address-family ipv4 vrf nepal-bank
    > 
    > redistribute ospf 2
    > 
    > exit-address-family
```

# R2
>
>ip cef
>
>mpls ip
>
>mpls ldp router-id loopback 0
>
>
>int f 0/0
>
>ip address 192.168.12.2 255.255.255.0
>
>mpls ip
>
>no shut
>
>int f 1/0
>
>ip address 192.168.23.2 255.255.255.0
>
>mpls ip
>
>no shut
>
>exit
>
>int loop 0
>
>ip address 2.2.2.2 255.255.255.255
>
>exit
>
>router ospf 1
>
>network 192.168.12.0 0.0.0.255 area 0
>
>network 192.168.23.0 0.0.0.255 area 0
>
>network 2.2.2.2 0.0.0.0 area 0
>
>exit


# R3

>ip cef
>
>mpls ip
>
>mpls ldp router-id loopback 0
>
>int f 1/0
>
>mpls ip
>
>ip address 192.168.23.3 255.255.255.0
>
>no shut
>
>exit
>
>int f 0/0
>
>mpls ip
>
>ip address 192.168.34.3 255.255.255.0
>
>no shut
>
>exit
>
>interface loop 0
>
>ip add 3.3.3.3 255.255.255.255
>
>exit
>
>
>router ospf 1
>
>network 192.168.23.0 0.0.0.255 area 0
>
>network 192.168.34.0 0.0.0.255 area 0
>
>network 3.3.3.3 0.0.0.0 area 0
>
>exit

# R4

> ip cef
>
> mpls ip
>
> mpls ldp router-id loopback 0
>
> int f 0/0
>
> mpls ip
>
> ip address 192.168.34.4 255.255.255.0
>
> no shut
>
> exit
>
> vrf definition nepal-bank
>
> rd 100:1
> 
> address-family ipv4
>
> route-target both 1:1
> 
> vrf definition laxmi-sunrise-bank
>
> rd 100:2
> 
> address-family ipv4
>
> route-target both  2:2
> 
> interface f 1/0
>
> vrf forwarding nepal-bank
>
> ip address 192.168.47.4 255.255.255.0
>
> no shut
>
> exit
>
> interface f 2/0
>
> vrf forwarding laxmi-sunrise-bank
>
> ip address 192.168.48.4 255.255.255.0
>
> no shut
>
> exit
>
> int loop 0
>
> ip address 4.4.4.4 255.255.255.255
>
> no shut
>
> exit
>
>
> exit
>
>router ospf 2 vrf nepal-bank
>
> network 192.168.47.0 0.0.0.255 area 0
>
> redistribute bgp 100 subnets
> 
> router ospf 1
>
> network 4.4.4.4 0.0.0.0 area 0
>
> network 192.168.34.0 0.0.0.255 area 0
>
> router rip
> 
> version 2
>
> no auto-summary
>
> address-family ipv4 vrf laxmi-sunrise-bank
>
> network 192.168.48.0
>
> redistribute bgp metric 1
> 
> no auto-summary
>
> version 2
>
>exit-address-family
>
> router bgp 100

> bgp log-neighbor-changes

> network 4.4.4.4 mask 255.255.255.255

> neighbor 1.1.1.1 remote-as 100

> neighbor 1.1.1.1 update-source Loopback0

> neighbor 1.1.1.1 next-hop-self

> 

> address-family vpnv4

> neighbor 1.1.1.1 activate

> neighbor 1.1.1.1 send-community extended

> exit-address-family
>
> address-family ipv4 vrf laxmi-sunrise-bank

> redistribute rip
>
> exit-address-family
>
> address-family ipv4 vrf nepal-bank

> redistribute ospf 2

> exit-address-family



# R5
>
>int f 1/0
>
>no shut
>
>ip add 192.168.15.5 255.255.255.0
>
>exit
>
>int loop 0
>
>ip add 10.10.10.10 255.255.255.255
>
>int loop 1
>
>ip add 11.11.11.11 255.255.255.255
>
>int loop 2
>
>ip add 12.12.12.12 255.255.255.255
>
>exit
>
>router ospf 1
>
>network 10.10.10.10 0.0.0.0 area 0
>
>network 11.11.11.11 0.0.0.0 area 0
>
>network 12.12.12.12 0.0.0.0 area 0
>
>network 192.168.15.0 0.0.0.255 area 0
>
>exit


# R6
>
>int f 2/0
>
>no shut
>
>ip add 192.168.16.6 255.255.255.0
>
>exit
>
>int loop 0
>
>ip add 10.10.10.10 255.255.255.255
>
>int loop 1
>
>ip add 11.11.11.11 255.255.255.255
>
>int loop 2
>
>ip add 12.12.12.12 255.255.255.255
>
>exit
>
>router rip
>
>no auto
>
>version 2
>
>network 10.0.0.0
>
>network 11.0.0.0
>
>network 12.0.0.0 
>
>network 192.168.16.0
>
>exit

# R7

>
>int f 1/0
>
>no shut
>
>ip add 192.168.47.7 255.255.255.0
>
>exit
>
>int loop 0
>
>ip add 20.20.20.20 255.255.255.255
>
>int loop 1
>
>ip add 30.30.30.30 255.255.255.255
>
>int loop 2
>
>ip add 40.40.40.40 255.255.255.255
>
>exit
>
>router ospf 1
>
>network 20.20.20.20 0.0.0.0 area 0
>
>network 30.30.30.30 0.0.0.0 area 0
>
>network 40.40.40.40 0.0.0.0 area 0
>
>network 192.168.47.0 0.0.0.255 area 0
>
>exit


# R8

>int f 2/0
>
>no shut
>
>ip add 192.168.48.8 255.255.255.0
>
>exit
>
>
>int loop 0
>
>ip add 20.20.20.20 255.255.255.255
>
>int loop 1
>
>ip add 30.30.30.30 255.255.255.255
>
>int loop 2
>
>ip add 40.40.40.40 255.255.255.255
>
>exit
>
>router rip
>
>no auto
>
>version 2
>
>network 20.0.0.0
>
>network 30.0.0.0
>
>network 40.0.0.0 
>
>network 192.168.48.0
>
>exit
>
