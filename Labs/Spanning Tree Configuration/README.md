STP Configuration Lab
Task Overview
In this lab, I worked with Spanning Tree Protocol (STP) across four switches and two VLANs.
The main goals were to identify the existing STP topology, manually control the Root Bridge for each VLAN, influence Root Port selection using cost and port priority, and configure PortFast and BPDU Guard on end-device ports.

**1. Checking the Current STP Topology**
I used the following command on each switch:
**show spanning-tree**
This allowed me to check:
Which switch was the Root Bridge
The role of each port
The STP state of each port
I found that SW2 was initially the Root Bridge, which was confirmed by:
"This bridge is the root"
Since SW2 was the Root Bridge, its active STP ports were Designated Ports and were in the Forwarding state.

**2. Configuring Root Bridges for Each VLAN**
I configured SW1 as:
Primary Root Bridge for VLAN 1
Secondary Root Bridge for VLAN 2
On SW1:
**configure terminal**
**spanning-tree vlan 1 root primary**
**spanning-tree vlan 2 root secondary**
On SW2, I configured the opposite:
**configure terminal**
**spanning-tree vlan 2 root primary**
**spanning-tree vlan 1 root secondary**
This gave each VLAN a planned Root Bridge instead of relying on the automatically elected switch.
I then used show spanning-tree again to verify the new STP topology and port roles.

**3. Changing STP Cost**
On SW4, I increased the VLAN 1 cost of interface F0/2:
**interface FastEthernet0/2**
**spanning-tree vlan 1 cost 100**
After increasing the cost, SW4 selected F0/1 instead of F0/2 as its Root Port.
This demonstrated that STP prefers the path with the lowest total Root Path Cost.
By making F0/2 more expensive, the alternative path became preferable.

**4. Changing Port Priority**
On SW1 F0/1, I changed the VLAN 1 port priority:
**interface FastEthernet0/1**
**spanning-tree vlan 1 port-priority 240**
This did not cause SW3 to select a different Root Port.
The reason is that STP first compares the Root Path Cost. Port priority is only used later as a tie-breaker when better criteria are equal.
In simplified order, STP prefers:
Lowest Root Path Cost
Lowest upstream / sender Bridge ID
Lowest sender Port ID, which includes port priority
Since SW3 already had a better path based on cost, changing the port priority did not change its Root Port.

**5. Configuring PortFast and BPDU Guard**
Finally, I configured the ports connected directly to PCs on SW3 and SW4.
On F0/3:
**interface FastEthernet0/3**
**spanning-tree portfast**
**spanning-tree bpduguard enable**
PortFast allows an end-device port to move to the Forwarding state quickly instead of waiting through the normal STP transition.
BPDU Guard protects the network by disabling the port if it unexpectedly receives a BPDU, which can help prevent an unauthorized switch from affecting the STP topology.
I verified the configuration using:
**show running-config**
Everything was configured and working correctly.


**What I Learned:**
This lab helped me understand how STP selects a Root Bridge and Root Ports, and how I can manually influence those decisions.
I practiced using Root Bridge priority, path cost, and port priority, and understood that STP uses cost before lower-level tie-breakers such as port priority.
I also learned the purpose of PortFast and BPDU Guard and why they are useful on ports connected to end devices rather than other switches.