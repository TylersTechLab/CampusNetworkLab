UNDER CONSTRUCTION -- VERY ROUGH 


<h1>Campus Network</h1>

<h2>Topology</h2>

incomplete rough
![09_27_22_02_55_12](https://user-images.githubusercontent.com/112909705/192413115-ec80d143-8b67-4ca4-aa1c-62c98c4bb95e.png)





<h2>Description</h2>
Tier-3 and Tier-2 Campus Networks.  

This lab configures many different features such as:
- IP addressing
- VLANs
- Inter-VLAN routing 
- HSRP 
- OSPF 
- EtherChannels 




<br />


<h2>Environments Used </h2>

- <b>Cisco Packet Tracer</b>


## Addressing
...

## Access Layer

I renamed all of the networking devices in the topology, as well as in the Cisco IOS software from their defaults. Other than being easier to read, I did this easily discover which ports were connected to which devices and their ports by using CDP, in case there was no topology to reference.



DSW1's interface that is is connected to ASW1 is Fas 0/1 
ASW1's interface that is connected to DSW1's interface is Fas 0/3
DSW2's interface that is connected to ASW1 is Fas 0/1
ASW1's interface that is connected to DSW2's interface is 0/4

![09_25_22_18_56_42](https://user-images.githubusercontent.com/112909705/192560070-d44b4a6c-2aa7-4d7d-a723-c231104a221e.png)

DSW1's interface that is connected to ASW2 is Fas 0/2
ASW2's interface that is is connected to DSW1 is Fas 0/3
DSW2's interface that is connected to ASW2 is Fas 0/2
ASW2's interface that is connected to DSW2 is Fas 0/4 

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

![09_26_22_15_34_22](https://user-images.githubusercontent.com/112909705/192560812-7d56d6ec-0a3f-46c7-bfe4-7ea9c346ab50.png)

The same IP routing capabilities and SVI configurations have been applied to DSW2

![09_26_22_15_46_46](https://user-images.githubusercontent.com/112909705/192560922-6f0179d3-ae40-4afb-833e-1230496c29cb.png)


Almost all of the configurations for the access layer are complete. The only thing remaining is setting up some load balancing capabilities to help even out the workload, as well as some redundancy in case one of the L3 switches fail.  

The FHRP protocol that will be used is HSRP. Setting up HSRP will give each VLAN a VIP, which will be the default gateway, and also allow for inter-vlan routing and being able to communicate with subnets outside of the access layer.  

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

##Distribution Layer 






