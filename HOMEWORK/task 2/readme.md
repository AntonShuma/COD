### TASK-2

## Underlay. OSPF

### Задача:

- Настроить протокол OSPF для Underlay сети
- Проверить связанность между устройствами

## Выполнение:

### Схема сети

![](pics/OSPF.PNG)


### Конфигурация оборудования

- #### [leaf-1](conf/leaf-1.conf)
  
```
hostname LEAF-1
!
spanning-tree mode mstp
!
interface Ethernet1
   description TO_SPINE-1
   no switchport
   ip address 10.1.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 l0w89E/+c20=
!
interface Ethernet2
   description TO_SPINE-2
   no switchport
   ip address 10.2.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Loopback1
   description UNDERLAY
   ip address 10.0.1.1/32
   ip ospf area 0.0.0.0
!
router ospf 100
   router-id 10.0.1.1
   bfd default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
```

- #### [leaf-2](conf/leaf-2.conf)
   
```
hostname LEAF-2
!
spanning-tree mode mstp
!
interface Ethernet1
   description TO_SPINE-1
   no switchport
   ip address 10.1.2.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 l0w89E/+c20=
!
interface Ethernet2
   description TO_SPINE-2
   no switchport
   ip address 10.2.2.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Loopback1
   description UNDERLAY
   ip address 10.0.2.1/32
   ip ospf area 0.0.0.0
!
router ospf 100
   router-id 10.0.1.1
   bfd default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
```

- #### [leaf-3](conf/leaf-3.conf)
  
```
hostname LEAF-3
!
spanning-tree mode mstp
!
interface Ethernet1
   description TO_SPINE-1
   no switchport
   ip address 10.1.3.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 l0w89E/+c20=
!
interface Ethernet2
   description TO_SPINE-2
   no switchport
   ip address 10.2.3.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Loopback1
   description UNDERLAY
   ip address 10.0.3.1/32
   ip ospf area 0.0.0.0
!
router ospf 100
   router-id 10.0.1.1
   bfd default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
```


- #### [spine-1](conf/spine-1.conf)

  
```
hostname SPINE-1
!
spanning-tree mode mstp
!
interface Ethernet1
   description TO_LEAF-1
   no switchport
   ip address 10.1.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 l0w89E/+c20=
!
interface Ethernet2
   description TO_LEAF-2
   no switchport
   ip address 10.1.2.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Ethernet3
   description TO_LEAF-3
   no switchport
   ip address 10.1.3.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Loopback1
   description UNDERLAY
   ip address 10.1.0.1/32
   ip ospf area 0.0.0.0
!
router ospf 100
   router-id 10.0.1.1
   bfd default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
```


- #### [spine-2](conf/spine-2.conf)
  
```
hostname SPINE-2
!
spanning-tree mode mstp
!
interface Ethernet1
   description TO_LEAF-1
   no switchport
   ip address 10.2.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 l0w89E/+c20=
!
interface Ethernet2
   description TO_LEAF-2
   no switchport
   ip address 10.2.2.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Ethernet3
   description TO_LEAF-3
   no switchport
   ip address 10.2.3.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 v1srhOqHBBs=
!
interface Loopback1
   description UNDERLAY
   ip address 10.2.0.1/32
   ip ospf area 0.0.0.0
!
router ospf 100
   router-id 10.0.1.1
   bfd default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
```


### Проверка связанности

```

SPINE-1#sho ip ospf neighbor 
Neighbor ID     Instance VRF      Pri State                  Dead Time   Address         Interface
10.0.1.1        100      default  1   FULL                   00:00:32    10.1.1.1        Ethernet1
10.0.2.1        100      default  1   FULL                   00:00:33    10.1.2.1        Ethernet2
10.0.3.1        100      default  0   FULL                   00:00:32    10.1.3.1        Ethernet3

SPINE-1#sho ip route

 O        10.0.1.1/32 [110/20] via 10.1.1.1, Ethernet1
 O        10.0.2.1/32 [110/20] via 10.1.2.1, Ethernet2
 O        10.0.3.1/32 [110/20] via 10.1.3.1, Ethernet3
 O        10.2.0.1/32 [110/30] via 10.1.1.1, Ethernet1
                               via 10.1.2.1, Ethernet2
                               via 10.1.3.1, Ethernet3
 O        10.2.1.0/31 [110/20] via 10.1.1.1, Ethernet1
 O        10.2.2.0/31 [110/20] via 10.1.2.1, Ethernet2
 O        10.2.3.0/31 [110/20] via 10.1.3.1, Ethernet3

```

```

SPINE-2#sho ip ospf ne
Neighbor ID     Instance VRF      Pri State                  Dead Time   Address         Interface
10.0.1.1        100      default  0   FULL                   00:00:35    10.2.1.1        Ethernet1
10.0.2.1        100      default  1   FULL                   00:00:34    10.2.2.1        Ethernet2
10.0.3.1        100      default  0   FULL                   00:00:37    10.2.3.1        Ethernet3


SPINE-2#sho ip ro ospf

 O        10.0.1.1/32 [110/20] via 10.2.1.1, Ethernet1
 O        10.0.2.1/32 [110/20] via 10.2.2.1, Ethernet2
 O        10.0.3.1/32 [110/20] via 10.2.3.1, Ethernet3
 O        10.1.0.1/32 [110/30] via 10.2.1.1, Ethernet1
                               via 10.2.2.1, Ethernet2
                               via 10.2.3.1, Ethernet3
 O        10.1.1.0/31 [110/20] via 10.2.1.1, Ethernet1
 O        10.1.2.0/31 [110/20] via 10.2.2.1, Ethernet2
 O        10.1.3.0/31 [110/20] via 10.2.3.1, Ethernet3

```
