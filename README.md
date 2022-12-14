UNDER CONSTRUCTION -- VERY ROUGH 


<h1>Campus Network</h1>

<h2>Topology</h2>

incomplete rough
![09_29_22_14_02_35](https://user-images.githubusercontent.com/112909705/193038548-84d5457a-c40f-4d56-b07d-87efffc7c41b.png)


(update after setting up the OSPF)



<h2>Description</h2>
Tier-3 and Tier-2 Campus Networks.  

This lab configures many different features such as:
- IP addressing
- VLANs
- Inter-VLAN routing 
- HSRP 
- OSPF 
- EtherChannels 
- NAT

(make more detailed)


<br />


<h2>Environments Used </h2>

- <b>Cisco Packet Tracer</b>


## Addressing
...

## Access Layer

(Go through, make more detailed and clean) 

I renamed all of the networking devices in the topology, as well as in the Cisco IOS software from their defaults. Other than being easier to read, I did this easily discover which ports were connected to which devices and their ports by using CDP, in case there was no topology to reference.



DSW1 Fas 0/1 <---> ASW1 Fas 0/3

DSW2 Fas 0/1 <---> ASW1 Fas 0/4

![09_25_22_18_56_42](https://user-images.githubusercontent.com/112909705/192560070-d44b4a6c-2aa7-4d7d-a723-c231104a221e.png)


DSW1 Fas 0/2 <---> ASW2 Fas 0/3

DSW2 Fas 0/2 <---> ASW2 Fas 0/4

![09_25_22_19_05_35](https://user-images.githubusercontent.com/112909705/192560188-c835dd7e-1ecc-4ee6-afe1-9041324fb012.png)

Configuring VLANs for ports in Access Switches facing local hosts. Same steps have been applied on ASW2 and ASW3, except different VLANs. 

ASW1: VLAN 10
ASW2: VLAN 20
ASW3: VLAN 30

![09_26_22_02_45_55](https://user-images.githubusercontent.com/112909705/192560289-a0c6a9f9-eced-48cf-8bb7-4674c9b8dcc7.png)

![09_26_22_02_52_20](https://user-images.githubusercontent.com/112909705/192560338-cafa3ddf-df3e-44fe-99de-de1db1a80c83.png)


Next is enabling links to be trunks between the Access and Distribution Switches.
First by starting with the access switches. The same commands have been applied to all access switches, but to their respective ports. The DSWs have encapsulation currently on "auto".

![09_26_22_13_52_26](https://user-images.githubusercontent.com/112909705/192560418-16b6b235-1e57-4b06-9cc4-075c02a2f60d.png)

![09_26_22_13_57_35](https://user-images.githubusercontent.com/112909705/192560481-fe803f05-01d1-43eb-a6e0-35e793451be8.png)

Next on the Distribution Switches, quickly creating VLANs 10-30. This has been applied to all DSWs. 

![09_26_22_14_12_54](https://user-images.githubusercontent.com/112909705/192560628-6a4929a7-fe39-4eb1-9380-cdc15ad3bd04.png)

PC0 and PC1, which are both in VLAN 10, are able to talk to one another. However inter-vlan routing is not working because routing has not been enabled on the L3 Distribution Switches. 

![09_26_22_14_29_53](https://user-images.githubusercontent.com/112909705/192560717-80599200-9da5-4062-a690-3c8f2c45bdd3.png)

Using IP Routing to enable L3 capabilities and then configuring SVIs on DSW1 with IP addresses, subnet masks, and then enabling the interfaces. 

The routing table is disabled initially on Cisco Layer 3 switches, and it must be enabled to perform Layer 3 routing. 

![09_26_22_15_34_22](https://user-images.githubusercontent.com/112909705/192560812-7d56d6ec-0a3f-46c7-bfe4-7ea9c346ab50.png)

The same IP routing capabilities and SVI configurations have been applied to DSW2

![09_26_22_15_46_46](https://user-images.githubusercontent.com/112909705/192560922-6f0179d3-ae40-4afb-833e-1230496c29cb.png)


Almost all of the configurations for the access layer are complete. The only thing remaining is setting up some load balancing capabilities to help even out the workload, as well as some redundancy in case one of the L3 switches fail.  

The FHRP protocol that will be used is HSRP. Setting up HSRP will give each VLAN a VIP, which will be the default gateway, and also allow for inter-VLAN routing and being able to communicate with subnets outside of the access layer.  

Since there are 3 VLANs and 3 subnets, 3 HSRP groups will need to be created on each DSW, and each group with be configured with their respective virtual IP, which is the default gateway IP for each host and each SVI. 

HSRPv2 will be used. The multicast address for v2 is 224.0.0.102.

Also the Virtual MAC address used in v2 will be: 0000.0c9f.fXXX (XXX = HSRP group number)

If configuring virtual IP address in group one, the virtual MAC address would be 0000.0c9f.f001.

These are the running configs of the VLANs for DSW1 and DSW2. For some reason, priority 100 (maybe the default priority) was not displaying in the running config file, so 99 was used instead. 

![09_27_22_15_15_37](https://user-images.githubusercontent.com/112909705/192561006-36519c9c-c56f-497e-aaba-5fdd9b18b770.png)


![09_27_22_15_16_21](https://user-images.githubusercontent.com/112909705/192561203-6eab0deb-82c7-4f2d-afab-9dbfa63616cb.png)

Looking at the standby configs, we see more detail and can also see which are actually active and standby based on how we set the priority. 

VLAN10 on DSW1 will be Active.
VLAN20 on DSW1 will be Active.
VLAN30 on DSW1 will be Standby. 

VLAN10 on DSW2 will be Standby
VLAN20 on DSW2 will be Standby
VLAN30 on DSW2 will be Active

![09_27_22_15_45_09](https://user-images.githubusercontent.com/112909705/192561306-1b677fda-d8b0-43bd-b63e-5af8251d72dd.png)

Also a quick test of inter-VLAN routing. It now works since FHRP has been configured. The first ping fails as the ARP process goes through, and then we get replies from the target host.


![09_27_22_15_20_10](https://user-images.githubusercontent.com/112909705/192561400-5c8c383c-c0c6-437d-9e8f-114227dd20c2.png)

## Distribution Layer 

Similar to what was done in the Access Layer, using CDP in the Distribution Layer to discover other devices attached to DSW1. In this instance, we want to know which Core Switches are attached. 

DSW1 Fas 0/4 <---> CSW1 Gig 1/0/1 

DSW1 Fas 0/5 <---> CSW1 Gig 1/0/2 

DSW1 Fas 0/6 <---> CSW2 Gig 1/0/3

![09_27_22_18_35_24](https://user-images.githubusercontent.com/112909705/192667964-df4efba3-3e92-4845-baba-049b52115479.png)


Looking for Core Switches from DSW2. 

DSW2 Fas 0/4 <---> CSW2 Gig 1/0/1

DSW2 Fas 0/5 <---> CSW2 Gig 1/0/2

DSW2 Fas 0/6 <---> CSW1 Gig 1/0/3

![09_28_22_00_47_02](https://user-images.githubusercontent.com/112909705/192668014-98ddf182-2161-43fc-8692-39d8dccabefe.png)

Configuring EtherChannel, as well as IP addresses for the EtherChannel group. 

![09_28_22_01_02_50](https://user-images.githubusercontent.com/112909705/192668062-7fbbb4d7-997d-4d1f-bcae-e5100cf29dfc.png)

D denotes "Down", and the EtherChannel is not operational. The side CSW1 that is facing DSW1 needs to be configured with EtherChannel. 

![09_28_22_01_12_26](https://user-images.githubusercontent.com/112909705/192668102-a8df42fa-4f33-4d76-8fde-b98ac58ebaf6.png)


Checking the EtherChannel settings, things are now operational. 

![09_28_22_01_12_45](https://user-images.githubusercontent.com/112909705/192668127-9bbfc32f-dc80-468c-ad9e-59a59b978ceb.png)


The EtherChannel has been configured Between DSW2 and CSW2 in almost an identical way as DSW1 CSW2. The only difference being the addressing, where DSW2 is 192.168.255.9/30 and CSW2 being 192.168.255.10/30.

Some times when setting up EtherChannel on both sides, the port links will go red on one of the sides. Normally shutting down the EtherChannel interface and enabling it will resolve this. 


The final step for the Distribution Layer is configuring the single links from DSW1 to CSW2 and DSW2 to CSW1. 


DSW1 Fas 0/6 <---> CSW2 Gig 1/0/3

![09_28_22_02_11_20](https://user-images.githubusercontent.com/112909705/192668160-1f4b894c-292f-456c-99bc-7a1b0edfb6d5.png)

DSW2 Fas 0/6 <---> CSW1 Gig 1/0/6

![09_28_22_02_30_17](https://user-images.githubusercontent.com/112909705/192668212-172543d0-7d39-4291-8763-62a03aa22764.png)

DSW1 being configured.

![09_28_22_02_16_07](https://user-images.githubusercontent.com/112909705/192668255-3bf6f872-dd88-4e1c-b0c3-fc65ce1eeb47.png)

CSW2 being configured.

![09_28_22_02_24_11](https://user-images.githubusercontent.com/112909705/192668303-f2797fae-dd9e-4e3b-9dfa-b4e5e3d723eb.png)

The same configurations have been applied to DSW2 and CSW1, except the IP addressing. CSW1: 192.168.255.14/30   DSW2: 192.168.255.13/30 


## Core Layer 


Using CDP to find out which ports from CSW1 are linked to CSW2.

CSW1 Gig 1/0/3 <---> CSW2 Gig 1/0/6
CSW1 Gig 1/0/4 <---> CSW2 Gig 1/0/4

![09_28_22_18_49_45](https://user-images.githubusercontent.com/112909705/193035900-34a94005-cb21-4d41-a150-5512d4b7d0a9.png)

![09_28_22_19_10_13](https://user-images.githubusercontent.com/112909705/193036014-e3a52ebf-9e61-4041-bd1b-3288b6b61a1f.png)

Configuring OSPF on all L3 switches and on the Router

![09_29_22_13_25_43](https://user-images.githubusercontent.com/112909705/193036123-c3cd8c88-7fb7-4c2a-a1fb-ff46ed4d12c0.png)

Example of routes learned

![09_29_22_13_38_30](https://user-images.githubusercontent.com/112909705/193036275-72911058-52be-4f9f-93fb-2ccd2ce1fb4b.png)

With the routes being advertised and established, now all of the host PCs can ping the Router. 

![09_29_22_13_33_31](https://user-images.githubusercontent.com/112909705/193036379-cdfc6456-1ca0-40ae-b184-a2645dc37d28.png)





