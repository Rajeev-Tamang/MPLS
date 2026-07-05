![INTRA-AS-MPLS-L3-VPN](INTRA%20AS%20MPLS%20L3%20VPN.jpg)

# Intra-AS MPLS Layer 3 VPN Lab

This lab demonstrates an Intra-AS MPLS Layer 3 VPN topology.

* R1 and R4 are Provider Edge (PE) routers.
* R2 and R3 are Provider (P) routers.
* R5 and R7 belong to the `nepal-bank` VRF.
* R6 and R8 belong to the `laxmi-sunrise-bank` VRF.
* OSPF is used inside the provider MPLS core.
* MP-BGP VPNv4 exchanges VPN routes between PE routers.
* `nepal-bank` uses OSPF between CE and PE routers.
* `laxmi-sunrise-bank` uses RIP version 2 between CE and PE routers.

> Configure each router from global configuration mode using `configure terminal`.

---

# R1 — Provider Edge Router

```cisco
hostname R1

ip cef
mpls ip
mpls ldp router-id Loopback0 force

vrf definition nepal-bank
 rd 100:1
 address-family ipv4
  route-target both 1:1
 exit-address-family

vrf definition laxmi-sunrise-bank
 rd 100:2
 address-family ipv4
  route-target both 2:2
 exit-address-family

interface FastEthernet1/0
 vrf forwarding nepal-bank
 ip address 192.168.15.1 255.255.255.0
 no shutdown
exit

interface FastEthernet2/0
 vrf forwarding laxmi-sunrise-bank
 ip address 192.168.16.1 255.255.255.0
 no shutdown
exit

interface FastEthernet0/0
 ip address 192.168.12.1 255.255.255.0
 mpls ip
 no shutdown
exit

interface Loopback0
 ip address 1.1.1.1 255.255.255.255
exit

router ospf 1
 router-id 1.1.1.1
 network 1.1.1.1 0.0.0.0 area 0
 network 192.168.12.0 0.0.0.255 area 0
exit

router ospf 2 vrf nepal-bank
 router-id 1.1.1.1
 network 192.168.15.0 0.0.0.255 area 0
 redistribute bgp 100 subnets
exit

router rip
 version 2
 no auto-summary
 address-family ipv4 vrf laxmi-sunrise-bank
  network 192.168.16.0
  redistribute bgp 100 metric 1
  no auto-summary
  version 2
 exit-address-family
exit

router bgp 100
 bgp log-neighbor-changes
 neighbor 4.4.4.4 remote-as 100
 neighbor 4.4.4.4 update-source Loopback0

 address-family ipv4
  network 1.1.1.1 mask 255.255.255.255
 exit-address-family

 address-family vpnv4
  neighbor 4.4.4.4 activate
  neighbor 4.4.4.4 send-community extended
 exit-address-family

 address-family ipv4 vrf nepal-bank
  redistribute ospf 2
 exit-address-family

 address-family ipv4 vrf laxmi-sunrise-bank
  redistribute rip
 exit-address-family
exit
```

---

# R2 — Provider Core Router

```cisco
hostname R2

ip cef
mpls ip
mpls ldp router-id Loopback0 force

interface FastEthernet0/0
 ip address 192.168.12.2 255.255.255.0
 mpls ip
 no shutdown
exit

interface FastEthernet1/0
 ip address 192.168.23.2 255.255.255.0
 mpls ip
 no shutdown
exit

interface Loopback0
 ip address 2.2.2.2 255.255.255.255
exit

router ospf 1
 router-id 2.2.2.2
 network 192.168.12.0 0.0.0.255 area 0
 network 192.168.23.0 0.0.0.255 area 0
 network 2.2.2.2 0.0.0.0 area 0
exit
```

---

# R3 — Provider Core Router

```cisco
hostname R3

ip cef
mpls ip
mpls ldp router-id Loopback0 force

interface FastEthernet1/0
 ip address 192.168.23.3 255.255.255.0
 mpls ip
 no shutdown
exit

interface FastEthernet0/0
 ip address 192.168.34.3 255.255.255.0
 mpls ip
 no shutdown
exit

interface Loopback0
 ip address 3.3.3.3 255.255.255.255
exit

router ospf 1
 router-id 3.3.3.3
 network 192.168.23.0 0.0.0.255 area 0
 network 192.168.34.0 0.0.0.255 area 0
 network 3.3.3.3 0.0.0.0 area 0
exit
```

---

# R4 — Provider Edge Router

```cisco
hostname R4

ip cef
mpls ip
mpls ldp router-id Loopback0 force

vrf definition nepal-bank
 rd 100:1
 address-family ipv4
  route-target both 1:1
 exit-address-family

vrf definition laxmi-sunrise-bank
 rd 100:2
 address-family ipv4
  route-target both 2:2
 exit-address-family

interface FastEthernet0/0
 ip address 192.168.34.4 255.255.255.0
 mpls ip
 no shutdown
exit

interface FastEthernet1/0
 vrf forwarding nepal-bank
 ip address 192.168.47.4 255.255.255.0
 no shutdown
exit

interface FastEthernet2/0
 vrf forwarding laxmi-sunrise-bank
 ip address 192.168.48.4 255.255.255.0
 no shutdown
exit

interface Loopback0
 ip address 4.4.4.4 255.255.255.255
exit

router ospf 1
 router-id 4.4.4.4
 network 4.4.4.4 0.0.0.0 area 0
 network 192.168.34.0 0.0.0.255 area 0
exit

router ospf 2 vrf nepal-bank
 router-id 4.4.4.4
 network 192.168.47.0 0.0.0.255 area 0
 redistribute bgp 100 subnets
exit

router rip
 version 2
 no auto-summary
 address-family ipv4 vrf laxmi-sunrise-bank
  network 192.168.48.0
  redistribute bgp 100 metric 1
  no auto-summary
  version 2
 exit-address-family
exit

router bgp 100
 bgp log-neighbor-changes
 neighbor 1.1.1.1 remote-as 100
 neighbor 1.1.1.1 update-source Loopback0

 address-family ipv4
  network 4.4.4.4 mask 255.255.255.255
 exit-address-family

 address-family vpnv4
  neighbor 1.1.1.1 activate
  neighbor 1.1.1.1 send-community extended
 exit-address-family

 address-family ipv4 vrf nepal-bank
  redistribute ospf 2
 exit-address-family

 address-family ipv4 vrf laxmi-sunrise-bank
  redistribute rip
 exit-address-family
exit
```

---

# R5 — Nepal Bank Customer Edge Router

```cisco
hostname R5

interface FastEthernet1/0
 ip address 192.168.15.5 255.255.255.0
 no shutdown
exit

interface Loopback0
 ip address 10.10.10.10 255.255.255.255
exit

interface Loopback1
 ip address 11.11.11.11 255.255.255.255
exit

interface Loopback2
 ip address 12.12.12.12 255.255.255.255
exit

router ospf 1
 router-id 10.10.10.10
 network 10.10.10.10 0.0.0.0 area 0
 network 11.11.11.11 0.0.0.0 area 0
 network 12.12.12.12 0.0.0.0 area 0
 network 192.168.15.0 0.0.0.255 area 0
exit
```

---

# R6 — Laxmi Sunrise Bank Customer Edge Router

```cisco
hostname R6

interface FastEthernet2/0
 ip address 192.168.16.6 255.255.255.0
 no shutdown
exit

interface Loopback0
 ip address 10.10.10.10 255.255.255.255
exit

interface Loopback1
 ip address 11.11.11.11 255.255.255.255
exit

interface Loopback2
 ip address 12.12.12.12 255.255.255.255
exit

router rip
 version 2
 no auto-summary
 network 10.0.0.0
 network 11.0.0.0
 network 12.0.0.0
 network 192.168.16.0
exit
```

---

# R7 — Nepal Bank Customer Edge Router

```cisco
hostname R7

interface FastEthernet1/0
 ip address 192.168.47.7 255.255.255.0
 no shutdown
exit

interface Loopback0
 ip address 20.20.20.20 255.255.255.255
exit

interface Loopback1
 ip address 30.30.30.30 255.255.255.255
exit

interface Loopback2
 ip address 40.40.40.40 255.255.255.255
exit

router ospf 1
 router-id 20.20.20.20
 network 20.20.20.20 0.0.0.0 area 0
 network 30.30.30.30 0.0.0.0 area 0
 network 40.40.40.40 0.0.0.0 area 0
 network 192.168.47.0 0.0.0.255 area 0
exit
```

---

# R8 — Laxmi Sunrise Bank Customer Edge Router

```cisco
hostname R8

interface FastEthernet2/0
 ip address 192.168.48.8 255.255.255.0
 no shutdown
exit

interface Loopback0
 ip address 20.20.20.20 255.255.255.255
exit

interface Loopback1
 ip address 30.30.30.30 255.255.255.255
exit

interface Loopback2
 ip address 40.40.40.40 255.255.255.255
exit

router rip
 version 2
 no auto-summary
 network 20.0.0.0
 network 30.0.0.0
 network 40.0.0.0
 network 192.168.48.0
exit
```

---

# Verification Commands

Run these commands after configuration to verify MPLS, OSPF, BGP VPNv4, VRFs, and customer routes.

## Verify MPLS Core

```cisco
show mpls ldp neighbor
show mpls forwarding-table
show ip ospf neighbor
show ip route ospf
```

## Verify MP-BGP VPNv4 on R1 and R4

```cisco
show ip bgp vpnv4 all summary
show ip bgp vpnv4 all
show ip bgp vpnv4 vrf nepal-bank
show ip bgp vpnv4 vrf laxmi-sunrise-bank
```

## Verify VRF Routing Tables on R1 and R4

```cisco
show ip route vrf nepal-bank
show ip route vrf laxmi-sunrise-bank
show ip vrf
show ip vrf interfaces
```

## Test Connectivity

From R5, test Nepal Bank VPN connectivity to R7:

```cisco
ping 20.20.20.20
ping 30.30.30.30
ping 40.40.40.40
```

From R6, test Laxmi Sunrise Bank VPN connectivity to R8:

```cisco
ping 20.20.20.20
ping 30.30.30.30
ping 40.40.40.40
```

> R5 should reach R7 because both belong to the `nepal-bank` VPN.
> R6 should reach R8 because both belong to the `laxmi-sunrise-bank` VPN.
> Routes must remain separated between the two VRFs, even though some CE loopback IP addresses overlap.
