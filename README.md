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

## IF THE ROUTER OR L3SWITCH CONNECTED TO OTHER DEVICES

```
ROUTER1# configure terminal
ROUTER1(config)# Interface FastEthernet0/1
ROUTER1(config-if)# no switchport
ROUTER1(config-if)# ip address 192.168.30.1 255.255.255.0
ROUTER1(config-if)# ip route 0.0.0.0 0.0.0.0 192.168.30.2
```
