UNDER CONSTRUCTION -- VERY ROUGH 


<h1>Campus Network</h1>

<h2>Topology</h2>

incomplete rough
![09_26_22_14_44_02](https://user-images.githubusercontent.com/112909705/192295614-6acc280b-dafe-4394-88c5-020d8c5c1905.png)




<h2>Description</h2>
Tier-3 and Tier-2 Campus Networks.
<br />



<h2>Environments Used </h2>

- <b>Cisco Packet Tracer</b>

<h2>Network Walk-through</h2>

## Planned out network topology and addressing
...

## Initial Configurations

First off, I renamed all of the networking devices in the topology, as well as in the software of Cisco IOS. I did this to better find out which ports were connected to other devices by using CDP. 


DSW1's interface that is is connected to ASW1 is Fas 0/1 

ASW1's interface that is connected to DSW1's interface is Fas 0/3

DSW2's interface that is connected to ASW1 is Fas 0/1

ASW1's interface that is connected to DSW2's interface is 0/4

![09_25_22_18_56_42](https://user-images.githubusercontent.com/112909705/192293034-45111c01-b7fa-4b8f-b640-c5224796903b.png)


DSW1's interface that is connected to ASW2 is Fas 0/2

ASW2's interface that is is connected to DSW1 is Fas 0/3

DSW2's interface that is connected to ASW2 is Fas 0/2

ASW2's interface that is connected to DSW2 is Fas 0/4 

![09_25_22_19_05_35](https://user-images.githubusercontent.com/112909705/192293525-c92d41a8-44ba-4775-9f57-26bbe805e6f3.png)

DSW1's interface that is connected to ASW3 is Fas 0/3

ASW3's interface that is connected to DSW1 is Fas 0/3

DSW2's interface that is connected to ASW3 is Fas 0/3

ASW3's interface that is connected to DSW2 is Fas 0/4

![09_25_22_19_10_45](https://user-images.githubusercontent.com/112909705/192293734-e4dd963d-8a98-4579-84ac-53d6f3ce7975.png)

Configuring VLANs for ports in Access Switches facing local hosts. Same steps have been applied on ASW2 and ASW3, except different VLANs. 

ASW1: VLAN 10

ASW2: VLAN 20

ASW3: VLAN 30

![09_26_22_02_45_55](https://user-images.githubusercontent.com/112909705/192293960-3869b14e-bd07-46f5-868c-2792b69e2fcf.png)

![09_26_22_02_52_20](https://user-images.githubusercontent.com/112909705/192294204-d24802ca-3dc4-4ef6-8bbd-a76084b4343a.png)


Next is enabling links to be trunks between the Access and Distribution Switches.
First by starting with the access switches. The same commands have been applied to all access switches, but to their respective ports. The DSWs have encapsulation currently on "auto".

![09_26_22_13_52_26](https://user-images.githubusercontent.com/112909705/192294451-e27a54b2-13dc-4e2e-af5c-c29b0891e7f6.png)

![09_26_22_13_57_35](https://user-images.githubusercontent.com/112909705/192294519-66bb249f-971e-4583-9159-8f53cefc708d.png)

Next on the Distribution Switches, quickly creating VLANs 10-30. This has been applied to all DSWs. After the commands, they now exist with no ports assigned to them. 

![09_26_22_14_12_54](https://user-images.githubusercontent.com/112909705/192294723-67ca8453-b9d3-47e6-acd3-e77ecbc333e3.png)

At this point, PC0 and PC1, which are both in VLAN 10, are able to talk to one another. However inter-vlanning is not working at this point because routing has not been enabled on the L3 Distribution Switches. 

![09_26_22_14_29_53](https://user-images.githubusercontent.com/112909705/192294824-709be047-b52d-4eb1-9748-d147116e81cc.png)






