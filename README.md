# Virtual Local Area Network (VLAN)

VLANs improve network performance by separating large broadcast domains into smaller segments. A VLAN allows a network administrator to create logical groups of network devices. These devices act like they are in their own independent network, even if they share a common infrastructure with other VLANs.

## Creating, Assigning Port 

![Creating a VLAN](https://raw.githubusercontent.com/deliawolf/VLAN/4eca22d44b9729bf6948970b50cdd0af756e273a/1.Create%20VLAN.svg)
1. Creating a VLAN
note: always remember to create the VLAN in l3 switch when using l3switch
```
SWITCH1# configure terminal
   
SWITCH1(config)# vlan 10
SWITCH1(config-vlan)# name Sales
SWITCH1(config)# vlan 20   
SWITCH1(config-vlan)# name Voice
```

2. Assigning a Port
```
SWITCH1# configure terminal

SWITCH1(config)# interface FastEthernet 0/0 
SWITCH1(config-if)# switchport mode access  
SWITCH1(config-if)# switchport access vlan 10

SWITCH1(config)# interface FastEthernet 0/1
SWITCH1(config-if)# switchport mode access  
SWITCH1(config-if)# switchport voice vlan 20
```

3. Validating the VLAN
```
SWITCH1# show vlan id 10
SWITCH1# show vlan id 20
SWITCH1# show vlan brief
SWITCH1# show interface FastEthernet0/0 switchport
SWITCH1# show interface FastEthernet0/1 switchport
```

## Trunking with 802.1Q
![Trunking with 802.1Q](https://raw.githubusercontent.com/deliawolf/VLAN/2dc751a56635770e947748028b2e0e79dfae99b0/2.Trunk%20VLAN.svg)

Trunking must configured in both end, in this case is SWITCH1 and SWITCH2

1. SWITCH1
```
SWITCH1(config)# interface FastEthernet 0/2
SWITCH1(config-if)# switchport trunk encapsulation dot1q
SWITCH1(config-if)# switchport mode trunk
SWITCH1(config-if)# switchport trunk native vlan 1
SWITCH1(config-if)# switchport trunk allowed vlan add 1,10,20
```

2. SWITCH2
```
SWITCH2(config)# interface FastEthernet 0/2
SWITCH2(config-if)# switchport trunk encapsulation dot1q
SWITCH2(config-if)# switchport mode trunk
SWITCH2(config-if)# switchport trunk native vlan 1
SWITCH2(config-if)# switchport trunk allowed vlan add 1,10,20
```

3. Validating the Trunk
```
SWITCH1# show interfaces FastEthernet0/2 switchport
SWITCH2# show interfaces FastEthernet0/2 switchport
SwitchX# show interfaces trunk
```

## Inter-VLAN Routing

There is 3 Option to configure Inter-VLAN Routing
- Option 1: Router with separate Interface in Each VLAN / Traditional Inter-VLAN
- Option 2: Router on a Stick (using single physical interface)
- Option 3: Layer 3 Switch

1. Option 1: Router with separate Interface in Each VLAN / Traditional Inter-VLAN

   ![Option 1](https://raw.githubusercontent.com/deliawolf/VLAN/cdc1b8cfed0284ce09a7a6fe772a58f087f929b3/3.Traditional%20Inter-VLAN.svg)
   ```
   SWITCH1(config)# interface FastEthernet0/0
   SWITCH1(config-if)# switchport mode access
   SWITCH1(config-if)# switchport access vlan 10
   SWITCH1(config-if)# exit

   SWITCH1(config)# interface FastEthernet0/1
   SWITCH1(config-if)# switchport mode access
   SWITCH1(config-if)# switchport access vlan 20
   SWITCH1(config-if)# exit
   ```
   ```
   ROUTER1# configure terminal
   
   ROUTER1(config)# interface FastEthernet0/0
   ROUTER1(config-if)# ip address 192.168.10.1 255.255.255.0
   ROUTER1(config-if)# no shutdown
   ROUTER1(config-if)# exit
   
   ROUTER1(config)# interface FastEthernet0/1
   ROUTER1(config-if)# ip address 192.168.20.1 255.255.255.0
   ROUTER1(config-if)# no shutdown
   ROUTER1(config-if)# exit
   ```

3. Option 2: Router on a Stick (using single physical interface)

   ![Option 2](https://raw.githubusercontent.com/deliawolf/VLAN/cdc1b8cfed0284ce09a7a6fe772a58f087f929b3/4.Router%20on%20a%20stick%20VLAN.svg)
   ```
   SWITCH1(config)# interface FastEthernet 0/0
   SWITCH1(config-if)# switchport trunk encapsulation dot1q
   SWITCH1(config-if)# switchport mode trunk
   SWITCH1(config-if)# switchport trunk native vlan 1
   SWITCH1(config-if)# switchport trunk allowed vlan add 1,10,20
   ```
   ```
   ROUTER1# configure terminal
   ROUTER1(config)# interface FastEthernet0/0.10
   ROUTER1(config-subif)# encapsulation dot1q 10
   ROUTER1(config-subif)# ip address 192.168.10.1 255.255.255.0
   ROUTER1(config-subif)# exit
   
   ROUTER1(config)# interface FastEthernet0/1.20
   ROUTER1(config-subif)# encapsulation dot1q 20
   ROUTER1(config-subif)# ip address 192.168.20.1 255.255.255.0
   ROUTER1(config-subif)# exit
   
   ROUTER1(config)# interface FastEthernet0/1
   ROUTER1(config-if)# no shutdown
   ROUTER1(config-if)# exit
   ```

4. Option 3: Layer 3 Switch

   ![Option 3](https://raw.githubusercontent.com/deliawolf/VLAN/cdc1b8cfed0284ce09a7a6fe772a58f087f929b3/5.%20L3%20VLAN.svg)
   ```
   L3SWITCH1# configure terminal
   L3SWITCH1(config)# ip routing
   
   L3SWITCH1(config)# interface vlan 10
   L3SWITCH1(config-if)# ip address 192.168.10.1 255.255.255.0
   L3SWITCH1(config-if)# no shitdown
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface vlan 20
   L3SWITCH1(config-if)# ip address 192.168.20.1 255.255.255.0
   L3SWITCH1(config-if)# no shitdown
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface FastEthernet0/0
   L3SWITCH1(config-if)# switchport mode access
   L3SWITCH1(config-if)# switchport access vlan 10
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface FastEthernet0/1
   L3SWITCH1(config-if)# switchport mode access
   L3SWITCH1(config-if)# switchport access vlan 20
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface FastEthernet0/2
   L3SWITCH1(config-if)# switchport mode access
   L3SWITCH1(config-if)# switchport access vlan 10
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface FastEthernet0/3
   L3SWITCH1(config-if)# switchport mode access
   L3SWITCH1(config-if)# switchport access vlan 20
   L3SWITCH1(config-if)# exit
   ```

6. Option3: Layer 3 Switch with L2 Switch

   ![Option 3.1](https://raw.githubusercontent.com/deliawolf/VLAN/cdc1b8cfed0284ce09a7a6fe772a58f087f929b3/6.%20L3%20VLAN.svg)
   ```
   L3SWITCH1# configure terminal
   L3SWITCH1(config)# ip routing

   L3SWITCH1(config)# interface FastEthernet0/0
   L3SWITCH1(config-if)# switchport trunk encapsulation dot1q
   L3SWITCH1(config-if)# switchport mode trunk
   L3SWITCH1(config-if)# switchport trunk native vlan 1
   L3SWITCH1(config-if)# switchport trunk allowed vlan add 1,10,20
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface vlan10
   L3SWITCH1(config-if)# ip address 192.168.10.1 255.255.255.0
   L3SWITCH1(config-if)# no shutdown
   L3SWITCH1(config-if)# exit

   L3SWITCH1(config)# interface vlan20
   L3SWITCH1(config-if)# ip address 192.168.20.1 255.255.255.0
   L3SWITCH1(config-if)# no shutdown
   L3SWITCH1(config-if)# exit
   ```
## ADDITIONAL COMMANDS
```
Switch(config-if)# switchport voice vlan VLAN_ID
Switch(config)# vlan vlan-id-list VLAN_IDs
Switch(config-if)# switchport nonegotiate
Switch(config-if)# switchport trunk native vlan 10
```
make default vlan tagged, normally default vlan is untagged
```
Switch(config-if)# vlan dot1q tag native
```

## IF THE ROUTER OR L3SWITCH CONNECTED TO OTHER DEVICES

```
ROUTER1# configure terminal
ROUTER1(config)# Interface FastEthernet0/1
ROUTER1(config-if)# no switchport
ROUTER1(config-if)# ip address 192.168.30.1 255.255.255.0
ROUTER1(config-if)# ip route 0.0.0.0 0.0.0.0 192.168.30.2
```
## BEST PRACTICE



    1. Use Separate VLANs for Different Network Segments: Assign separate VLANs for different network segments or purposes, such as data, voice, management, guest, or IoT devices. This helps improve security, manageability, and traffic segmentation.

    2. Plan VLANs in Advance: Before deploying VLANs, plan and document the VLAN assignments, including VLAN numbers, names, and their respective purposes. This helps ensure consistency and ease of management.

    3. Avoid Using VLAN 1 for User Traffic: Cisco devices often have VLAN 1 as the default VLAN, which is typically used for management purposes. It is recommended to avoid using VLAN 1 for user traffic to improve security and reduce the risk of unauthorized access.

    4. Native VLAN Design Considerations: When using 802.1Q trunking, consider designating a specific VLAN as the native VLAN. Ensure that the native VLAN is not used for user traffic and implement appropriate security measures, such as VLAN access control lists (VACLs), to prevent unauthorized access.

    5. Implement VLAN Access Control Lists (VACLs): VACLs allow you to filter traffic within a VLAN and provide an additional layer of security. Use VACLs to control traffic flow between VLANs or to restrict certain types of traffic within a VLAN.

    6. Implement VLAN Pruning: If your network has VLANs that are not required on certain trunk links, enable VLAN pruning to prevent unnecessary VLAN traffic from traversing those links. This helps optimize bandwidth utilization.

    7. Implement VLAN Spanning Tree Protocol (STP) Best Practices: Configure VLAN STP parameters, such as root bridge selection, to ensure a stable and efficient spanning tree topology for each VLAN.

    8. Implement VLAN Naming Conventions: Use descriptive and standardized names for VLANs to improve clarity and ease of management. Consider a naming convention that aligns with your organization's naming standards.
