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
