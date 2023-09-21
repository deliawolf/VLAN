# Virtual Local Area Network (VLAN)

VLANs improve network performance by separating large broadcast domains into smaller segments. A VLAN allows a network administrator to create logical groups of network devices. These devices act like they are in their own independent network, even if they share a common infrastructure with other VLANs.

##Creating a VLAN

![Creating a VLAN](https://raw.githubusercontent.com/deliawolf/VLAN/4eca22d44b9729bf6948970b50cdd0af756e273a/1.Create%20VLAN.svg)
```diff
+ **SwitchX#** +configure terminal   
**SwitchX(config)#** vlan 2   
**SwitchX(config-vlan)#** name Sales
**SwitchX(config)#** vlan 20   
**SwitchX(config-vlan)#** name Voice
```
