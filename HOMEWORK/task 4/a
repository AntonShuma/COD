# TASK-4

## Underlay. eBGP

### Задача:

- Настроить протокол eBGP для Underlay сети
- Проверить связанность между устройствами

## Выполнение:

### Схема сети

![](pics/eBGP.PNG)

### Конфигурация оборудования

- #### [leaf-1](conf/leaf-1.conf)


```
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
interface Loopback1
   description UNDERLAY
   ip address 10.0.1.1/32
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.0.1.1/32
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
router bgp 65101
   router-id 10.0.1.1
   timers bgp 3 9
   maximum-paths 2 ecmp 2
   neighbor SPINES peer group
   neighbor SPINES remote-as 65001
   neighbor SPINES bfd
   neighbor SPINES allowas-in 1
   neighbor SPINES rib-in pre-policy retain all
   neighbor SPINES password 7 wMeqR2znTcw=
   neighbor SPINES send-community
   neighbor SPINES maximum-routes 1000
   neighbor 10.1.1.0 peer group SPINES
   neighbor 10.2.1.0 peer group SPINES
   redistribute connected route-map RM_CONNECTED
   !
   address-family ipv4
      neighbor SPINES activate
!

```

- #### [leaf-2](conf/leaf-2.conf)


```
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
interface Loopback1
   description UNDERLAY
   ip address 10.0.2.1/32
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.0.2.1/32
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
router bgp 65102
   router-id 10.0.2.1
   timers bgp 3 9
   maximum-paths 2 ecmp 2
   neighbor SPINES peer group
   neighbor SPINES remote-as 65001
   neighbor SPINES bfd
   neighbor SPINES allowas-in 1
   neighbor SPINES rib-in pre-policy retain all
   neighbor SPINES password 7 wMeqR2znTcw=
   neighbor SPINES send-community
   neighbor SPINES maximum-routes 1000
   neighbor 10.1.2.0 peer group SPINES
   neighbor 10.2.2.0 peer group SPINES
   redistribute connected route-map RM_CONNECTED
   !
   address-family ipv4
      neighbor SPINES activate
!

```

- #### [leaf-3](conf/leaf-3.conf)


```
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
interface Loopback1
   description UNDERLAY
   ip address 10.0.3.1/32
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.0.3.1/32
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
router bgp 65103
 router-id 10.0.3.1
   timers bgp 3 9
   maximum-paths 2 ecmp 2
   neighbor SPINES peer group
   neighbor SPINES remote-as 65001
   neighbor SPINES bfd
   neighbor SPINES allowas-in 1
   neighbor SPINES rib-in pre-policy retain all
   neighbor SPINES password 7 wMeqR2znTcw=
   neighbor SPINES send-community
   neighbor SPINES maximum-routes 1000
   neighbor 10.1.3.0 peer group SPINES
   neighbor 10.2.3.0 peer group SPINES
   redistribute connected route-map RM_CONNECTED
   !
   address-family ipv4
      neighbor SPINES activate
!

```

- #### [spine-1](conf/spine-1.conf)


```
!
interface Ethernet1
   description TO_LEAF-1
   no switchport
   ip address 10.1.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet2
   description TO_LEAF-2
   no switchport
   ip address 10.1.2.0/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet3
   description TO_LEAF-3
   no switchport
   ip address 10.1.3.0/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Loopback1
   description UNDERLAY
   ip address 10.1.0.1/32
!
ip prefix-list PL_LOOPBACK
   seq 10 permit 10.1.0.1/32
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list PL_LOOPBACK
!
peer-filter LEAFS
   10 match as-range 65101-65103 result accept
!
router bgp 65001
   router-id 10.1.0.1
   timers bgp 3 9
   maximum-paths 2 ecmp 2
   bgp listen range 10.1.0.0/22 peer-group LEAFS peer-filter LEAFS
   neighbor LEAFS peer group
   neighbor LEAFS bfd
   neighbor LEAFS rib-in pre-policy retain all
   neighbor LEAFS password 7 325Pwtva6k8=
   neighbor LEAFS send-community
   neighbor LEAFS maximum-routes 1000
   redistribute connected route-map RM_CONNECTED
   !
   address-family ipv4
     neighbor LEAFS activate
!

```

- #### [spine-2](conf/spine-2.conf)


```
!
interface Ethernet1
   description TO_LEAF-1
   no switchport
   ip address 10.2.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet2
   description TO_LEAF-2
   no switchport
   ip address 10.2.2.0/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Ethernet3
   description TO_LEAF-3
   no switchport
   ip address 10.2.3.0/31
   bfd interval 100 min-rx 100 multiplier 3
!
interface Loopback1
   description UNDERLAY
   ip address 10.2.0.1/32
!
!
ip prefix-list LOOPBACK
   seq 10 permit 10.2.0.1/32
!
route-map RM_CONNECTED permit 10
   match ip address prefix-list LOOPBACK
!
peer-filter LEAFS
   10 match as-range 65101-65103 result accept
!
router bgp 65001
   router-id 10.2.0.1
   timers bgp 3 9
   maximum-paths 2 ecmp 2
   bgp listen range 10.2.0.0/22 peer-group LEAFS peer-filter LEAFS
   neighbor LEAFS peer group
   neighbor LEAFS bfd
   neighbor LEAFS password 7 325Pwtva6k8=
   neighbor LEAFS send-community
   neighbor LEAFS maximum-routes 1000
   redistribute connected route-map RM_CONNECTED
 !
   address-family ipv4
      neighbor LEAFS activate
!


```

### Проверка связанности устройств по протоколу BGP

- #### spine-1

```
SPINE-1#sho ip bgp su
BGP summary information for VRF default
Router identifier 10.1.0.1, local AS number 65001
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.1.1         4  65101            136       137    0    0 00:06:30 Estab   2      1
  10.1.2.1         4  65102            116       117    0    0 00:05:32 Estab   2      1
  10.1.3.1         4  65103             41        42    0    0 00:01:47 Estab   2      1
SPINE-1#sho bfd peers 
VRF name: default
-----------------
DstAddr       MyDisc    YourDisc  Interface/Transport    Type           LastUp 
--------- ----------- ----------- -------------------- ------- ----------------
10.1.1.1  4049432517  1547006195        Ethernet1(15)  normal   07/08/24 09:41 
10.1.2.1  3948116969  1506691744        Ethernet2(16)  normal   07/08/24 09:42 
10.1.3.1  3702439773  3443207286        Ethernet3(17)  normal   07/08/24 09:46 


SPINE-1#ping 10.0.1.1 source 10.1.0.1
PING 10.0.1.1 (10.0.1.1) from 10.1.0.1 : 72(100) bytes of data.
80 bytes from 10.0.1.1: icmp_seq=1 ttl=64 time=10.7 ms
80 bytes from 10.0.1.1: icmp_seq=2 ttl=64 time=7.98 ms
80 bytes from 10.0.1.1: icmp_seq=3 ttl=64 time=4.50 ms
80 bytes from 10.0.1.1: icmp_seq=4 ttl=64 time=7.96 ms
80 bytes from 10.0.1.1: icmp_seq=5 ttl=64 time=16.5 ms

--- 10.0.1.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 44ms
rtt min/avg/max/mdev = 4.500/9.542/16.538/4.017 ms, ipg/ewma 11.171/10.324 ms
SPINE-1#ping 10.0.2.1 source 10.1.0.1
PING 10.0.2.1 (10.0.2.1) from 10.1.0.1 : 72(100) bytes of data.
80 bytes from 10.0.2.1: icmp_seq=1 ttl=64 time=6.12 ms
80 bytes from 10.0.2.1: icmp_seq=2 ttl=64 time=4.63 ms
80 bytes from 10.0.2.1: icmp_seq=3 ttl=64 time=5.78 ms
80 bytes from 10.0.2.1: icmp_seq=4 ttl=64 time=6.55 ms
80 bytes from 10.0.2.1: icmp_seq=5 ttl=64 time=7.32 ms

--- 10.0.2.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 31ms
rtt min/avg/max/mdev = 4.634/6.085/7.326/0.895 ms, ipg/ewma 7.943/6.164 ms
SPINE-1#ping 10.0.3.1 source 10.1.0.1
PING 10.0.3.1 (10.0.3.1) from 10.1.0.1 : 72(100) bytes of data.
80 bytes from 10.0.3.1: icmp_seq=1 ttl=64 time=8.94 ms
80 bytes from 10.0.3.1: icmp_seq=2 ttl=64 time=5.31 ms
80 bytes from 10.0.3.1: icmp_seq=3 ttl=64 time=22.9 ms
80 bytes from 10.0.3.1: icmp_seq=4 ttl=64 time=18.7 ms
80 bytes from 10.0.3.1: icmp_seq=5 ttl=64 time=12.5 ms

--- 10.0.3.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 43ms
rtt min/avg/max/mdev = 5.319/13.706/22.904/6.401 ms, pipe 3, ipg/ewma 10.843/11.508 ms

```

- #### spine-2

```
SPINE-2#sho ip bgp sum
BGP summary information for VRF default
Router identifier 10.2.0.1, local AS number 65001
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.2.1.1         4  65101            773       772    0    0 00:38:16 Estab   4      1
  10.2.2.1         4  65102            755       754    0    0 00:37:21 Estab   4      1
  10.2.3.1         4  65103            680       679    0    0 00:33:36 Estab   4      1
SPINE-2#sho bfd peers
VRF name: default
-----------------
DstAddr       MyDisc    YourDisc  Interface/Transport    Type           LastUp 
--------- ----------- ----------- -------------------- ------- ----------------
10.2.1.1  1371908217   114906037        Ethernet1(15)  normal   07/08/24 09:41 
10.2.2.1  3612157739    56171156        Ethernet2(16)  normal   07/08/24 09:42 
10.2.3.1  1506584399  1677998344        Ethernet3(17)  normal   07/08/24 09:46 


SPINE-2#ping 10.0.1.1 source 10.2.0.1
PING 10.0.1.1 (10.0.1.1) from 10.2.0.1 : 72(100) bytes of data.
80 bytes from 10.0.1.1: icmp_seq=1 ttl=64 time=6.06 ms
80 bytes from 10.0.1.1: icmp_seq=2 ttl=64 time=13.1 ms
80 bytes from 10.0.1.1: icmp_seq=3 ttl=64 time=9.09 ms
80 bytes from 10.0.1.1: icmp_seq=4 ttl=64 time=4.20 ms
80 bytes from 10.0.1.1: icmp_seq=5 ttl=64 time=4.19 ms

--- 10.0.1.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 38ms
rtt min/avg/max/mdev = 4.194/7.345/13.171/3.420 ms, pipe 2, ipg/ewma 9.503/6.511 ms
SPINE-2#ping 10.0.2.1 source 10.2.0.1
PING 10.0.2.1 (10.0.2.1) from 10.2.0.1 : 72(100) bytes of data.
80 bytes from 10.0.2.1: icmp_seq=1 ttl=64 time=8.44 ms
80 bytes from 10.0.2.1: icmp_seq=2 ttl=64 time=6.23 ms
80 bytes from 10.0.2.1: icmp_seq=3 ttl=64 time=9.11 ms
80 bytes from 10.0.2.1: icmp_seq=4 ttl=64 time=5.04 ms
80 bytes from 10.0.2.1: icmp_seq=5 ttl=64 time=5.65 ms

--- 10.0.2.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 37ms
rtt min/avg/max/mdev = 5.048/6.896/9.111/1.596 ms, ipg/ewma 9.250/7.600 ms
SPINE-2#ping 10.0.3.1 source 10.2.0.1
PING 10.0.3.1 (10.0.3.1) from 10.2.0.1 : 72(100) bytes of data.
80 bytes from 10.0.3.1: icmp_seq=1 ttl=64 time=8.69 ms
80 bytes from 10.0.3.1: icmp_seq=2 ttl=64 time=8.03 ms
80 bytes from 10.0.3.1: icmp_seq=3 ttl=64 time=7.47 ms
80 bytes from 10.0.3.1: icmp_seq=4 ttl=64 time=4.30 ms
80 bytes from 10.0.3.1: icmp_seq=5 ttl=64 time=3.98 ms

--- 10.0.3.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 36ms
rtt min/avg/max/mdev = 3.988/6.497/8.690/1.959 ms, ipg/ewma 9.159/7.451 ms

```
