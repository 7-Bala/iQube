## peer to peer


2 PCs  
	|  
connect directly  
	|  
ethernet crossover cable

same devices -> crossover  

different devices -> straight-through

PC -> PC = crossover  

PC -> hub/switch = straight-through

## IP config

PC -> Desktop -> IP Configuration

PC0 -> `10.10.10.1`  
PC1 -> `10.10.10.2`

same network -> communicate

## commands

`ipconfig` -> IP configuration

`ipconfig /all` -> detailed info -> Physical Address(MAC)

`ping <IP>` -> check connectivity

eg : `ping 10.10.10.2`

# Hub

hub -> central device for LAN

Layer 1 -> Physical Layer

packet comes in one port  
		|  
broadcasts to all other ports

no MAC table,no memory,no filtering

PC0 -> hub -> all other PCs

## hub problems

cheap,simple

bandwidth wastage,broadcasting,collisions,half duplex

half duplex -> transmit OR receive

# Switch

switch -> connects devices in LAN

Layer 2 -> Data Link Layer

has memory + MAC address table

packet  
|  
destination MAC  
|  
check MAC table  
|  
correct port  
|  
forward only to destination

mainly unicast

## MAC table

`Switch> enable`

`Switch# show mac-address-table`

MAC address -> port

# Hub vs Switch

hub -> Layer 1,no memory,broadcast,half duplex,less efficient

switch -> Layer 2,MAC address table,mainly unicast,full duplex,more efficient