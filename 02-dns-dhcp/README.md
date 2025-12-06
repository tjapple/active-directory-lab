## Goal
Assign DNS and DHCP roles on the Domain Controller to support Active Directory authentication, name resolution, dynamic DNS updates, and automatic IP assignment for domain-joined clients. 

## Architecture and Design 
* Dual-NIC DC architecture (NAT for Internet, Internal for AD network)
* Enable DNS and DHCP so clients only use the DC for these services. 

 
## Actions
* Set NIC ethernet settings:
  * Set Internet NIC with automatic DHCP and DNS (assigned from VirtualBox under NAT mode)
  * Set Internal NIC with private address 10.0.0.1 255.255.255.0 and point DNS to itself (DCs must always point DNS to themselves)
    
    <img width="452" height="404" alt="image" src="https://github.com/user-attachments/assets/9858f623-2574-4d4a-891b-2ff8334e513d" />
* Installed the Remote Access role on the server so that internal clients can still connect to the Internet
  * Assigned role and selected "enable Routing" during setup so the server can translate traffic between the two NICs
  * Assigned the public interface that will connect to the Internet in Tools -> Routing and Remote Access
* DNS configuration
  * Tools -> DNS -> added new reverse lookup zone with network ID 10.0.0
* Installed and configured DHCP server
  * Assigned the "DHCP Server" role to the server
  * Tools -> dhcp -> ipv4 -> added new scope, setting the name and specifying IP range and reservations.
    <img width="383" height="319" alt="image" src="https://github.com/user-attachments/assets/4b166e51-df37-4e2a-aced-2bf85cb27951" />

    * The screenshot shows the external Internet NIC first, with an IP address and gateway assigned by VirtualBox
    * the second NIC is the internal interface, which show its IP address and the DNS entry pointing to its own address



## Problems Encountered
* Client still connecting to LAN even though NIC settings were changed
  * Had to run *ipconfig /release* then *ipconfig /renew* -> DNS records now only list the DC IP address. Could have been caused by DHCP cache.
* Client not showing up in DHCP address leases
  * Server roles were not assigned in setup. Had to assign 003 Router, 006 DNS Servers, and 015 DNS Domain Name
    <img width="409" height="182" alt="image" src="https://github.com/user-attachments/assets/82a6423f-7475-42ae-9458-8c325f3cf0c4" />

* nslookup worked for "dc1" but not for the IP
  * Had to manually add a PTR record in the reverse lookup zone for the Internal NIC's IP. This is because DCs with static IPs do not automatically create PTR records. :(


## What I learned
* Reverse lookup zones are not automatically created for statically assigned server NICs. They must be added manually. 
* Forward DNS zones resolve names to IP, reverse zones resolve IP to name.
* How to set up RRAS to allow the lab DC to handle outbound NAT.
* How to assign address pools and IP reservations to a DHCP server.
* Releasing and renewing dhcp settings can solve many issues. 
