


### 		IPAM

| Hostname | Interface | IP | Description |
| :------: | :-------: | :----------: | :---------: |
| SPINE-1 | Loop1 | 10.1.0.1 | Underlay |
| SPINE-1 | Loop2 | 10.1.0.2 | Overlay |
| SPINE-1 | Eth1 | 10.1.1.0/31 | TO_LEAF-1 |
| SPINE-1 | Eth2 | 10.1.2.0/31 | TO_LEAF-2 |
| SPINE-1 | Eth3 | 10.1.3.0/31 | TO_LEAF-3 |
|          |          |              |            |
| SPINE-2 | Loop1 | 10.2.0.1 | Underlay |
| SPINE-2 | Loop2 | 10.2.0.2 | Overlay |
| SPINE-2 | Eth1 | 10.2.1.0/31 | TO_LEAF-1 |
| SPINE-2 | Eth2 | 10.2.2.0/31 | TO_LEAF-2 |
| SPINE-2 | Eth3 | 10.2.3.0/31 | TO_LEAF-3 |
|          |          |              |            |
| LEAF-1 | Loop1 | 10.0.1.1 | Underlay |
| LEAF-1 | Loop2 | 10.0.1.2 | Overlay |
| LEAF-1 | Eth1 | 10.1.1.1/31 | TO_SPINE-1 |
| LEAF-1 | Eth2 | 10.2.1.1/31 | TO_SPINE-2 |
|          |          |              |            |
| LEAF-2 | Loop1 | 10.0.2.1 | Underlay |
| LEAF-2 | Loop2 | 10.0.2.2 | Overlay |
| LEAF-2 | Eth1 | 10.1.2.1/31 | TO_SPINE-1 |
| LEAF-2 | Eth2 | 10.2.2.1/31 | TO_SPINE-2 |
|          |          |              |            |
| LEAF-3 | Loop1 | 10.0.3.1 | Underlay |
| LEAF-3 | Loop2 | 10.0.3.2 | Overlay |
| LEAF-3 | Eth1 | 10.1.3.1/31 | TO_SPINE-1 |
| LEAF-3 | Eth2 | 10.2.3.1/31 | TO_SPINE-2 |










