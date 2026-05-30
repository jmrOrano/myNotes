
*Scenario: Host A and Host B are  directly connected. No router.*
Case: 
- Both have a NIC(Network Interface Card), and therefore a MAC Address.
- Both are configured with an IP and a Subnet Mask (should be different topic)
```
Host A ------ - ------ Host B

Host A 
- a2a2
- 10.1.1.22
- 255.255.255.0
  
Host B
- b3b3
- 10.1.1.33
- 255.255.255.0
```

- Host A has some data to send to Host B
- Host A knows the IP address of Host B
- Host A can create a L3 header attach to the Data
```
Data / Layer3

Layer 3 (in OSI Model) includes the ff:
- Source IP [10.1.1.22]
- Destination Ip [10.1.1.33]
```

- But host A doesn't know Host's B MAC address
```
Data / Layer3 / Layer2

Layer 2(Hop to Hop) includes the ff:
- Source MAC [a2a2]
- Destination MAC [????]
```
- This is where the **ARP ( Address Resolution Protocol)** is use to link Layer3 and Layer2 and perfom the hop to hop delivery .

**How ARP works in this example?**
- Host A will shoot out an **ARP Request**
```
Host A --------ARP Req./Layer2-----------

an Arp Request Includes the ff:
- Host A ip address [10.1.1.22]
- a message or request to resolve which MAC address owns the destination IP addr[10.1.1.33]
```
- Notice that the ARP Request payload has a Layer 2 attached. It is meant to take the ARP request payload but it doesnt have a MAC Address of the target host yet. instead, has a broadcast as a destination address.
```
Host A --------ARP Req./Layer2-----------

That Layer 2 attached to ARP payload includes the ff:
- Source MAC
- A broadcast MAC Address which is [ffff.ffff.ffff.ffff]. Meant to send to everyone in the same local network.
```
