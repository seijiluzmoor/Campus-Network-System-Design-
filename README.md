# Campus-Area-Network-System-Design
### Author: Seiji Luzmoor
### Date: 8/21/2025
### Contact: Email smluzmoor@gmail.com | Linkedin (www.linkedin.com/in/seiji-luzmoor) 

This repository contains the design, configuration, and documentation for my Campus Area Network Design project. The goal of this project was to design and implement a secure, and scalable network infrastructure for a multi-building campus environment, incorporating various networking technologies and security best practices.

### 1. Project Overview
This project simulates a Campus Area Network  encompassing multiple departments/buildings, each requiring distinct network services and security policies. The design emphasizes high availability, following a hierarchical network design model. Key aspects include:
- Layer 2 & Layer 3 Redundancy: Implementing EtherChannel and Hot Standby Router Protocol (HSRP) to ensure network uptime and resilience.
- VLAN Segmentation: Logically separating departments and services for enhanced security, performance, and broadcast domain reduction.
- Wireless Connectivity: Providing secure and managed wireless access across the campus for various user groups (Corporate, Employees, External Auditors, Guest).
- Network Security: Implementing a wide range of security features including Cisco ASA Firewalls, Access Control Lists (ACLs), and switchport security to protect against common attacks.
- Remote Access: Securely enabling network management from remote locations using SSH and restricted access controls.

### 2. Network Diagram
A visual representation of the campus network topology is included in the repository.


### 3. Network Components & Services
The network infrastructure leverages a variety of Cisco hardware and common network services:
- Cisco IOS Devices: Routers, Multilayer Switches, and Access Layer Switches.
- Cisco ASA Firewalls: For perimeter security, zone enforcement (Inside, Outside, DMZ), and Network Address Translation (NAT).
- Cisco Wireless LAN Controller (WLC): Centralized management for wireless access points.
- Cisco Lightweight Access Points (LWAPs): For providing wireless connectivity.
- Internet Service Provider (ISP): External connectivity to the internet.
- Google Cloud Platform (GCP): Represents external cloud services or resources.
- Active Directory as DHCP Server: Centralized DHCP services for IP address management.
- DNS, Web, Email, FTP Servers: Core network services hosted within the campus.

### 4. Configuration Highlights
Below are the key configurations implemented in this project, categorized for clarity.
The network follows a 3 tier network architecture design, including:
- Core Layer: High-speed backbone for inter-VLAN routing and connecting distribution layers.
- Distribution Layer: Aggregates access layer switches, implements routing policies, and provides inter-VLAN routing (using Multilayer Switches and SVIs).
- Access Layer: Connects end-user devices and prevents different network attacks.

Initial Device Setup (Cisco IOS)
- Basic configuration applied to routers and switches includes hostname configuration, MOTD banners, secure enable and   line passwords (console and VTY), exec timeouts, logging synchronous, disabling IP domain lookup, setting IP domain name, creating local usernames and passwords, encrypting all passwords, and setting current clock time. Secure Shell (SSH) is enabled for remote access, with a standard ACL applied to VTY lines to restrict access.

Subnetting & IP Addressing
- Comprehensive subnetting was performed to allocate IP address ranges efficiently for all VLANs (LANs, WLANs, Management, etc.), point-to-point links, and server subnets, ensuring no overlap and optimal utilization.
  
VLANs and Trunking
- VLANs are extensively used for logical segmentation, and trunking protocols manage VLAN traffic across interconnected switches.
- VLAN Creation and Naming: Dedicated VLANs are created for Management (VLAN 10), Headquarters LAN (VLAN 20), Headquarters WLAN (VLAN 50), Branch LAN (VLAN 60), Branch WLAN (VLAN 90), and a BLACKHOLE VLAN (VLAN 199) for unused ports.
- VLAN Assignment: Access ports are assigned to specific VLANs based on the connected devices (e.g., LAN devices to VLAN 20/60, WLAN devices to VLAN 50/90, management devices to VLAN 10).
- Trunk Port Configuration: Interfaces connecting switches are configured as trunk ports, allowing multiple VLANs to traverse them. Specific VLANs are allowed or denied on trunk links to control traffic flow and enhance security.
- Configure Native VLAN: The native VLAN is explicitly configured on trunk links to prevent potential VLAN hopping attacks.

EtherChannel
- LACP (Link Aggregation Control Protocol) EtherChannel is used to bundle multiple physical links into a single logical link, providing increased bandwidth and redundancy between network devices.

Inter-VLAN Routing & DHCP
- Inter-VLAN routing is primarily handled by Multilayer Switches using Switched Virtual Interfaces (SVIs) and also by routers using Router-on-a-Stick configurations where appropriate. DHCP services are integrated with inter-VLAN routing.
- Inter-VLAN Routing: Utilized SVIs on Multilayer Switches and subinterfaces on routers for routing traffic between different VLANs.
- DHCP Server: Active Directory acts as the primary DHCP server for the campus network.
- DHCP Relay Agent: Configured on routing devices to forward DHCP requests from clients to the central Active Directory DHCP server.
- Inter-VLAN + DHCP: Combining inter-VLAN routing with DHCP services to dynamically assign IP addresses to devices in different VLANs.

High Availability (HSRP)
- HSRP (Hot Standby Router Protocol) provides gateway redundancy for end devices, ensuring continuous network access even if a primary gateway fails. Virtual IP addresses are configured for different VLANs, allowing seamless failover.

Routing Protocols (OSPF & Static)
- OSPF (Open Shortest Path First) is the primary Interior Gateway Protocol (IGP) for dynamic routing within the campus network, configured on firewalls, routers, and switches.
- A Default Static Route provides external connectivity to the Internet. 

Wireless Network
- The wireless network provides secure and segmented access for different user groups using a centralized Cisco Wireless LAN Controller (WLC) and Lightweight Access Points (LWAPs).
- Cisco Wireless LAN Controller (WLC): Manages and controls Lightweight Access Points (LWAPs), providing centralized configuration and monitoring.
- Multiple SSIDs with VLAN Mapping: Separate SSIDs are configured for different user groups, each mapped to a dedicated VLAN

Cisco ASA Firewalls
Provide security at the network perimeter and between internal security zones.
- Firewall Inside-Outside-DMZ Zones: The network is segmented into different security zones (Inside, Outside, DMZ) with distinct security policies enforced by the ASA.
- NAT & PAT: Dynamic Port Address Translation (PAT) is configured to allow multiple internal hosts to share a single public IP address for Internet access.
- Inspection Policies - ACL: Access Control Lists are extensively used to define granular traffic filtering rules, allowing or denying specific types of traffic based on source, destination, and port.

Security Measures
A set of security features are implemented across the network devices:
- Remote Access - SSH: Secure Shell (SSH) is enabled for secure remote management, with Telnet disabled.
- Secure All Unused Switchports: Unused physical switch ports are shut down and assigned to a blackhole VLAN.
- STP PortFast and BPDUGuard: Configured on access layer switchports to prevent STP convergence delays and protect against rogue switches.
- DHCP Snooping: Mitigates rogue DHCP server attacks by trusting only specific ports for DHCP responses.
- Dynamic ARP Inspection (DAI): Protects against ARP spoofing attacks by validating ARP packets.
- IP Source Guard: Prevents IP address spoofing by restricting incoming IP traffic.
- Port Security: Limits the number of MAC addresses on a switch port to prevent unauthorized devices from connecting.
- ACLs - Standard + Extended: Used for traffic filtering 
- ACLs for VTY - Remote Access: Applied to VTY lines to restrict SSH access to specific management subnets.
- Prevent Brute-force Attack - Router: Measures are configured to prevent brute-force login attempts.

Server Configurations
- Active Directory as DHCP Server: Centralized IP address assignment and management.
- DNS, Web, Email, FTP Servers: Provide essential services for internal and external communication within the campus.

