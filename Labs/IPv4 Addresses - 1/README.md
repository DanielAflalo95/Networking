## Router Configuration Lab Summary

1. Accessed the router and configured the hostname

   First, I accessed the router's CLI and entered the `enable` command to enter Privileged EXEC mode.

   I then used:

   `configure terminal`

   to enter Global Configuration mode.

   From there, I changed the router's hostname to R1 using:

   `hostname R1`

2. Reviewed the router interfaces

   I used:

   `do show ip interface brief`

   to view a summary of R1's interfaces, including their IP addresses and current status.

3. Configured and enabled the router interfaces

   I entered each interface using:

   `interface g0/x`

   I then configured the appropriate IP address and subnet mask using:

   `ip address X.X.X.X X.X.X.X`

   Based on the network prefixes in the topology, I used the following subnet masks:

   - `/8` → `255.0.0.0`
   - `/16` → `255.255.0.0`
   - `/24` → `255.255.255.0`

   After configuring the IP addresses, I enabled each interface using:

   `no shutdown`

   I also added a description to each interface using:

   `description TO SWx`

   where `SWx` represents the switch connected to that specific router interface.

4. Verified the interface configuration

   After configuring all interfaces, I used:

   `do show ip interface brief`

   again to verify that the correct IP addresses had been assigned and that the interfaces were enabled.

5. Reviewed and saved the router configuration

   I used:

   `do show running-config`

   to review the router's current configuration and confirm that the changes were applied correctly.

   I then saved the running configuration to the startup configuration using:

   `write`

   This ensures that the configuration will remain after the router is restarted.

6. Configured the PCs

   On each PC, I configured the appropriate:

   - IP address
   - Subnet mask
   - Default gateway

   I assigned each PC the IP address shown in the network topology and used the IP address of R1's interface on the corresponding network as the default gateway.

7. Tested connectivity between the networks

   Finally, I used the `ping` command from PC1 to test connectivity with PC2 and PC3.

   All ping tests were successful, confirming that the router interfaces and PC network settings were configured correctly and that communication between all three networks was working as expected.