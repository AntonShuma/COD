# TASK 7

## Overlay. VxLAN VPC

### Задача:

- Подключить клиентов 2-я линками к разным Leaf
- Настроить агрегированный канал со стороны клиента 
- Настроить VPC для работы в Overlay сети
- Проверить связанность между клиентами

## Выполнение:

### Схема сети

![](pics/MULTIH.PNG)

### Конфигурация оборудования

- #### [leaf-1](config/leaf-1.conf)

```
!
service routing protocols model multi-agent
!
hostname LEAF-1
!
spanning-tree mode mstp
!
vlan 100,200
!
vrf instance MULTICARTA
!
interface Ethernet1
   description TO_SPINE-1
   no switchport
   ip address 10.1.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet2
   description TO_SPINE-2
   no switchport
   ip address 10.2.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
   description TO_CLIENT-1
   switchport access vlan 100
!
interface Ethernet8
   description TO_CLIENT-1-2
   switchport access vlan 200
!
interface Loopback1
   description UNDERLAY
   ip address 10.0.1.1/32
!
interface Loopback2
   description OVERLAY
   ip address 10.0.1.2/32
!
!
interface Vlan100
   description OFFICE
   vrf MULTICARTA
   ip address 192.168.100.1/24
!
interface Vlan200
   description PUBLIC
   vrf MULTICARTA
   ip address 192.168.200.1/24
!
interface Vxlan1
   vxlan source-interface Loopback2
   vxlan udp-port 4789
   vxlan vlan 100 vni 10100
   vxlan vlan 200 vni 10200
   vxlan vrf MULTICARTA vni 11
!
ip virtual-router mac-address 00:00:00:00:00:0a
!
ip routing
ip routing vrf MULTICARTA
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.0.1.1/32
   seq 20 permit 10.0.1.2/32
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
router bgp 65101
   router-id 10.0.1.1
   timers bgp 3 9
   maximum-paths 2 ecmp 2
   neighbor L2EVPN peer group
   neighbor L2EVPN remote-as 65001
   neighbor L2EVPN update-source Loopback1
   neighbor L2EVPN ebgp-multihop 3
   neighbor L2EVPN send-community extended
   neighbor SPINES peer group
   neighbor SPINES remote-as 65001
   neighbor SPINES bfd
   neighbor SPINES allowas-in 1
   neighbor SPINES rib-in pre-policy retain all
   neighbor SPINES password 7 wMeqR2znTcw=
   neighbor SPINES send-community
   neighbor SPINES maximum-routes 1000
   neighbor 10.1.0.1 peer group L2EVPN
   neighbor 10.1.1.0 peer group SPINES
   neighbor 10.2.0.1 peer group L2EVPN
   neighbor 10.2.1.0 peer group SPINES
   redistribute connected route-map RM_CONNECTED
   !
   vlan 100
      rd 65101:10100
      route-target both 100:100
      redistribute learned
   !
   vlan 200
      rd 65101:10200
      route-target both 200:200
      redistribute learned
   !
   address-family evpn
      neighbor L2EVPN activate
   !
   address-family ipv4
      neighbor SPINES activate
   !
   vrf MULTICARTA
      rd 65101:11
      route-target import evpn 11:11
      route-target export evpn 11:11
!
end

```

- #### [leaf-2](config/leaf-2.conf)

```
! device: LEAF-2 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname LEAF-2
!
spanning-tree mode mstp
no spanning-tree vlan-id 1000
!
vlan 100,200
!
vlan 1000
   trunk group MLAG
!
vrf instance MULTICARTA
!
interface Port-Channel1
   description MLAG_LINK
   switchport mode trunk
   switchport trunk group MLAG
!
interface Port-Channel10
   description TO_SRV
   switchport trunk allowed vlan 100,200
   switchport mode trunk
   mlag 10
!
interface Ethernet1
   description TO_SPINE-1
   no switchport
   ip address 10.1.2.1/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet2
   description TO_SPINE-2
   no switchport
   ip address 10.2.2.1/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet3
   description MLAG_LINK
   channel-group 1 mode active
!
interface Ethernet4
   description MLAG_LINK
   channel-group 1 mode active
!
interface Ethernet5
   description TO_SRV
   channel-group 10 mode active
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   description UNDERLAY
   ip address 10.0.2.1/32
!
interface Loopback2
   description OVERLAY
   ip address 10.0.2.2/32
!
interface Management1
!
interface Vlan100
   description OFFICE
   vrf MULTICARTA
   ip address 192.168.100.1/24
!
interface Vlan200
   description PUBLIC
   vrf MULTICARTA
   ip address 192.168.200.1/24
!
interface Vlan1000
   description MLAG_PEER
   ip address 172.16.0.0/31
!
interface Vxlan1
   vxlan source-interface Loopback2
   vxlan udp-port 4789
   vxlan vlan 100 vni 10100
   vxlan vlan 200 vni 10200
   vxlan vrf MULTICARTA vni 11
   vxlan virtual-vtep local-interface Loopback2
!
ip virtual-router mac-address 00:00:00:00:00:0b
!
ip routing
ip routing vrf MULTICARTA
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.0.2.1/32
   seq 20 permit 10.0.2.2/32
!
mlag configuration
   domain-id MLAG
   local-interface Vlan1000
   peer-address 172.16.0.1
   peer-link Port-Channel1
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
router bgp 65102
   router-id 10.0.2.1
   timers bgp 3 9
   maximum-paths 4 ecmp 4
   neighbor L2EVPN peer group
   neighbor L2EVPN remote-as 65001
   neighbor L2EVPN update-source Loopback1
   neighbor L2EVPN ebgp-multihop 3
   neighbor L2EVPN send-community extended
   neighbor SPINES peer group
   neighbor SPINES remote-as 65001
   neighbor SPINES bfd
   neighbor SPINES allowas-in 1
   neighbor SPINES rib-in pre-policy retain all
   neighbor SPINES password 7 wMeqR2znTcw=
   neighbor SPINES send-community
   neighbor SPINES maximum-routes 1000
   neighbor 10.1.0.1 peer group L2EVPN
   neighbor 10.1.2.0 peer group SPINES
   neighbor 10.2.0.1 peer group L2EVPN
   neighbor 10.2.2.0 peer group SPINES
   redistribute connected route-map RM_CONNECTED
   !
   vlan 100
      rd 65102:10100
      route-target both 100:100
      redistribute learned
   !
   vlan 200
      rd 65102:10200
      route-target both 200:200
      redistribute learned
   !
   address-family evpn
      neighbor L2EVPN activate
   !
   address-family ipv4
      neighbor SPINES activate
   !
   vrf MULTICARTA
      rd 65102:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      redistribute connected
!
end
```

- #### [leaf-3](config/leaf-3.conf)

```

! device: LEAF-3 (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname LEAF-3
!
spanning-tree mode mstp
no spanning-tree vlan-id 1000
!
vlan 100,200
!
vlan 1000
   trunk group MLAG
!
vrf instance MULTICARTA
!
interface Port-Channel1
   description MLAG_LINK
   switchport mode trunk
   switchport trunk group MLAG
!
interface Port-Channel10
   description TO_SRV
   switchport trunk allowed vlan 100,200
   switchport mode trunk
   mlag 10
!
interface Ethernet1
   description TO_SPINE-1
   no switchport
   ip address 10.1.3.1/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet2
   description TO_SPINE-2
   no switchport
   ip address 10.2.3.1/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet3
   description MLAG_LINK
   channel-group 1 mode active
!
interface Ethernet4
   description MLAG_LINK
   channel-group 1 mode active
!
interface Ethernet5
   description TO_SRV
   channel-group 10 mode active
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   description UNDERLAY
   ip address 10.0.3.1/32
!
interface Loopback2
   description OVERLAY
   ip address 10.0.2.2/32
!
interface Management1
!
interface Vlan100
   description OFFICE
   vrf MULTICARTA
   ip address 192.168.100.1/24
!
interface Vlan200
   description PUBLIC
   vrf MULTICARTA
   ip address 192.168.200.1/24
!
interface Vlan1000
   description MLAG_PEER
   ip address 172.16.0.1/31
!
interface Vxlan1
   vxlan source-interface Loopback2
   vxlan udp-port 4789
   vxlan vlan 100 vni 10100
   vxlan vlan 200 vni 10200
   vxlan vrf MULTICARTA vni 11
   vxlan virtual-vtep local-interface Loopback2
!
ip virtual-router mac-address 00:00:00:00:00:0b
!
ip routing
ip routing vrf MULTICARTA
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.0.3.1/32
   seq 20 permit 10.0.2.2/32
!
mlag configuration
   domain-id MLAG
   local-interface Vlan1000
   peer-address 172.16.0.0
   peer-link Port-Channel1
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
router bgp 65102
   router-id 10.0.3.1
   timers bgp 3 9
   maximum-paths 4 ecmp 4
   neighbor L2EVPN peer group
   neighbor L2EVPN remote-as 65001
   neighbor L2EVPN update-source Loopback1
   neighbor L2EVPN ebgp-multihop 3
   neighbor L2EVPN send-community extended
   neighbor SPINES peer group
   neighbor SPINES remote-as 65001
   neighbor SPINES bfd
   neighbor SPINES allowas-in 1
   neighbor SPINES rib-in pre-policy retain all
   neighbor SPINES password 7 wMeqR2znTcw=
   neighbor SPINES send-community
   neighbor SPINES maximum-routes 1000
   neighbor 10.1.0.1 peer group L2EVPN
   neighbor 10.1.3.0 peer group SPINES
   neighbor 10.2.0.1 peer group L2EVPN
   neighbor 10.2.3.0 peer group SPINES
   redistribute connected route-map RM_CONNECTED
   !
   vlan 100
      rd 65102:10100
      route-target both 100:100
      redistribute learned
   !
   vlan 200
      rd 65102:10200
      route-target both 200:200
      redistribute learned
   !
   address-family evpn
      neighbor L2EVPN activate
   !
   address-family ipv4
      neighbor SPINES activate
   !
   vrf MULTICARTA
      rd 65102:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      redistribute connected
!
end

```

- #### [SRV](config/SRV.conf)

```

! device: SRV (vEOS-lab, EOS-4.29.2F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname SRV
!
spanning-tree mode mstp
!
vlan 100,200
!
interface Port-Channel10
   description TO_LEAFS
   switchport trunk allowed vlan 100,200
   switchport mode trunk
!
interface Ethernet1
   channel-group 10 mode active
!
interface Ethernet2
   channel-group 10 mode active
!
interface Vlan100
   ip address 192.168.100.11/24
!
interface Vlan200
   ip address 192.168.200.11/24
!
ip routing
!
end

```

### Проверка связанности клиентов по L3

- #### leaf-1

```

LEAF-1#sho bgp evpn 
BGP routing table information for VRF default
Router identifier 10.0.1.1, local AS number 65101
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nextho
p

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 65101:10100 mac-ip 0050.7966.681f
                                 -                     -       -       0       i
 * >      RD: 65101:10100 mac-ip 0050.7966.681f 192.168.100.10
                                 -                     -       -       0       i
 * >      RD: 65101:10200 mac-ip 0050.7966.6823
                                 -                     -       -       0       i
 * >      RD: 65101:10200 mac-ip 0050.7966.6823 192.168.200.10
                                 -                     -       -       0       i
 * >Ec    RD: 65102:10100 mac-ip 503f.5d49.4bff
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:10100 mac-ip 503f.5d49.4bff
                                 10.0.2.2              -       100     0       65001 65102 i
 * >Ec    RD: 65102:10200 mac-ip 503f.5d49.4bff
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:10200 mac-ip 503f.5d49.4bff
                                 10.0.2.2              -       100     0       65001 65102 i
 * >Ec    RD: 65102:10100 mac-ip 503f.5d49.4bff 192.168.100.11
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:10100 mac-ip 503f.5d49.4bff 192.168.100.11
                                 10.0.2.2              -       100     0       65001 65102 i
 * >Ec    RD: 65102:10200 mac-ip 503f.5d49.4bff 192.168.200.11
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:10200 mac-ip 503f.5d49.4bff 192.168.200.11
                                 10.0.2.2              -       100     0       65001 65102 i
 * >      RD: 65101:10100 imet 10.0.1.2
                                 -                     -       -       0       i
 * >      RD: 65101:10200 imet 10.0.1.2
                                 -                     -       -       0       i
 * >Ec    RD: 65102:10100 imet 10.0.2.2
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:10100 imet 10.0.2.2
                                 10.0.2.2              -       100     0       65001 65102 i
 * >Ec    RD: 65102:10200 imet 10.0.2.2
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:10200 imet 10.0.2.2
                                 10.0.2.2              -       100     0       65001 65102 i
 * >Ec    RD: 65102:11 ip-prefix 192.168.100.0/24
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:11 ip-prefix 192.168.100.0/24
                                 10.0.2.2              -       100     0       65001 65102 i
 * >Ec    RD: 65102:11 ip-prefix 192.168.200.0/24
                                 10.0.2.2              -       100     0       65001 65102 i
 *  ec    RD: 65102:11 ip-prefix 192.168.200.0/24
                                 10.0.2.2              -       100     0       65001 65102 i

LEAF-1#sho vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP           Tunnel Type(s)
-------------- --------------
10.0.2.2       unicast, flood

Total number of remote VTEPS:  1


```

- #### leaf-2

```
LEAF-2#sho mlag 
MLAG Configuration:              
domain-id                          :                MLAG
local-interface                    :            Vlan1000
peer-address                       :          172.16.0.1
peer-link                          :       Port-Channel1
peer-config                        :          consistent
                                                       
MLAG Status:                     
state                              :              Active
negotiation status                 :           Connected
peer-link status                   :                  Up
local-int status                   :                  Up
system-id                          :   52:52:68:ab:94:56
dual-primary detection             :            Disabled
dual-primary interface errdisabled :               False
                                                       
MLAG Ports:                      
Disabled                           :                   0
Configured                         :                   0
Inactive                           :                   0
Active-partial                     :                   0
Active-full                        :                   1


LEAF-2#sho mlag interfaces detail 
                                   local/remote                                
mlag        state  local  remote   oper    config          last change  changes
----- ------------ ------ ------- ------ --------- -------------------- -------
  10  active-full   Po10    Po10  up/up   ena/ena   1 day, 8:29:21 ago       12



LEAF-2#sho bgp evpn 
BGP routing table information for VRF default
Router identifier 10.0.2.1, local AS number 65102
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nextho
p

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 65101:10100 mac-ip 0050.7966.681f
                                 10.0.1.2              -       100     0       65001 65101 i
 *  ec    RD: 65101:10100 mac-ip 0050.7966.681f
                                 10.0.1.2              -       100     0       65001 65101 i
 * >Ec    RD: 65101:10100 mac-ip 0050.7966.681f 192.168.100.10
                                 10.0.1.2              -       100     0       65001 65101 i
 *  ec    RD: 65101:10100 mac-ip 0050.7966.681f 192.168.100.10
                                 10.0.1.2              -       100     0       65001 65101 i
 * >      RD: 65102:10100 mac-ip 503f.5d49.4bff
                                 -                     -       -       0       i
 * >      RD: 65102:10200 mac-ip 503f.5d49.4bff
                                 -                     -       -       0       i
 * >      RD: 65102:10100 mac-ip 503f.5d49.4bff 192.168.100.11
                                 -                     -       -       0       i
 * >      RD: 65102:10200 mac-ip 503f.5d49.4bff 192.168.200.11
                                 -                     -       -       0       i
 * >Ec    RD: 65101:10100 imet 10.0.1.2
                                 10.0.1.2              -       100     0       65001 65101 i
 *  ec    RD: 65101:10100 imet 10.0.1.2
                                 10.0.1.2              -       100     0       65001 65101 i
 * >Ec    RD: 65101:10200 imet 10.0.1.2
                                 10.0.1.2              -       100     0       65001 65101 i
 *  ec    RD: 65101:10200 imet 10.0.1.2
                                 10.0.1.2              -       100     0       65001 65101 i
 * >      RD: 65102:10100 imet 10.0.2.2
                                 -                     -       -       0       i
 * >      RD: 65102:10200 imet 10.0.2.2
                                 -                     -       -       0       i
 * >      RD: 65102:11 ip-prefix 192.168.100.0/24
                                 -                     -       -       0       i
 * >      RD: 65102:11 ip-prefix 192.168.200.0/24

LEAF-2#sho vxlan vni 
VNI to VLAN Mapping for Vxlan1
VNI         VLAN       Source       Interface            802.1Q Tag
----------- ---------- ------------ -------------------- ----------
10100       100        static       Port-Channel10       100       
                                    Vxlan1               100       
10200       200        static       Port-Channel10       200       
                                    Vxlan1               200       

VNI to dynamic VLAN Mapping for Vxlan1
VNI       VLAN       VRF              Source       
--------- ---------- ---------------- ------------ 
11        4094       MULTICARTA       evpn         

LEAF-2#sho vxlan vtep 
Remote VTEPS for Vxlan1:

VTEP           Tunnel Type(s)
-------------- --------------
10.0.1.2       unicast, flood

Total number of remote VTEPS:  1


```

- #### SRV

```
SRV#ping 192.168.100.10
PING 192.168.100.10 (192.168.100.10) 72(100) bytes of data.
80 bytes from 192.168.100.10: icmp_seq=1 ttl=64 time=63.7 ms
80 bytes from 192.168.100.10: icmp_seq=2 ttl=64 time=66.6 ms
80 bytes from 192.168.100.10: icmp_seq=3 ttl=64 time=59.7 ms
80 bytes from 192.168.100.10: icmp_seq=4 ttl=64 time=48.3 ms
80 bytes from 192.168.100.10: icmp_seq=5 ttl=64 time=59.5 ms

--- 192.168.100.10 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 49ms
rtt min/avg/max/mdev = 48.312/59.611/66.695/6.242 ms, pipe 5, ipg/ewma 12.376/61.384 ms
SRV#ping 192.168.200.10
PING 192.168.200.10 (192.168.200.10) 72(100) bytes of data.
80 bytes from 192.168.200.10: icmp_seq=1 ttl=64 time=129 ms
80 bytes from 192.168.200.10: icmp_seq=2 ttl=64 time=128 ms
80 bytes from 192.168.200.10: icmp_seq=3 ttl=64 time=121 ms
80 bytes from 192.168.200.10: icmp_seq=4 ttl=64 time=113 ms
80 bytes from 192.168.200.10: icmp_seq=5 ttl=64 time=109 ms
```

- #### CLIENT-1

```
VPCS> ping 192.168.100.11

84 bytes from 192.168.100.11 icmp_seq=1 ttl=64 time=369.595 ms
84 bytes from 192.168.100.11 icmp_seq=2 ttl=64 time=71.575 ms
84 bytes from 192.168.100.11 icmp_seq=3 ttl=64 time=49.649 ms
84 bytes from 192.168.100.11 icmp_seq=4 ttl=64 time=65.137 ms
84 bytes from 192.168.100.11 icmp_seq=5 ttl=64 time=60.878 ms

VPCS> ping 192.168.200.11

84 bytes from 192.168.200.11 icmp_seq=1 ttl=64 time=338.711 ms
84 bytes from 192.168.200.11 icmp_seq=2 ttl=64 time=153.517 ms
84 bytes from 192.168.200.11 icmp_seq=3 ttl=64 time=50.814 ms
84 bytes from 192.168.200.11 icmp_seq=4 ttl=64 time=38.024 ms
84 bytes from 192.168.200.11 icmp_seq=5 ttl=64 time=55.512 ms

```
