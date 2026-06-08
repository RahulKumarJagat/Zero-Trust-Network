# Hotel Zero Trust Network (ZTN) Enforcement
**Enterprise Infrastructure Security using Micro-Segmentation & Layer 2 Mitigation**

[![Cisco Packet Tracer](https://img.shields.io/badge/Simulator-Cisco_Packet_Tracer-blue.svg)](https://www.netacad.com/courses/packet-tracer)
[![Architecture](https://img.shields.io/badge/Architecture-Collapsed_Core-success.svg)]()
[![Security](https://img.shields.io/badge/Security-Zero_Trust-red.svg)]()

## 📌 Project Overview
The **Hotel Zero Trust Network (ZTN) Enforcement** project is an enterprise-grade network infrastructure designed for a modern hospitality environment. Operating on the core cybersecurity paradigm of *"never trust, always verify"*, this project shifts away from outdated "castle-and-moat" perimeter defenses. 

It achieves strict compliance (e.g., PCI-DSS) through deep physical and logical micro-segmentation, managed centrally by a Layer 3 Multilayer Switch utilizing a highly efficient Collapsed Core architecture.

### **Academic Details**
* **Author:** Rahul Kumar Jagat (M0107)
* **Supervised By:** Dr. Manisha Chandrakar
* **Institution:** ITM University, Raipur

---

## 🎯 Objectives
1. **Enforce Strict Micro-Segmentation:** Divide the physical network into 18 isolated VLANs to trap potential payloads and prevent lateral movement.
2. **Implement Principle of Least Privilege:** Utilize Extended Access Control Lists (ACLs) to ensure departments only reach explicitly authorized servers.
3. **Mitigate Local-Loop Attacks:** Deploy hardware-level protections (DHCP Snooping, DAI, Port Security) to secure the Layer 2 broadcast domain.
4. **Identity-Driven Access:** Lock down physical switch edge ports using IEEE 802.1X RADIUS authentication.
5. **Guest Isolation:** Ensure unverified guest traffic is completely blinded to the sensitive corporate architecture.

---

## 🏗️ Network Architecture
* **Design Choice:** **Collapsed Core Topology**. Merges the Core and Distribution layers into a single Cisco 3560 Multilayer Switch (`SW-CORE`) to reduce packet latency and centralize routing/security.
* **IP Schema:**
  * `10.x.x.x/24` - Highly scalable Class A corporate network.
  * `172.16.x.x` - Isolated Class B guest network.
* **Key Hardware Simulated:**
  * Cisco 3560 Layer 3 Switch (`SW-CORE`)
  * Cisco 2960 Layer 2 Access Switches (`SW-B1`, etc.)
  * Cisco ASA 5506-X Firewall (`FW-ASA`)
  * Centralized RADIUS (`SRV-AAA`) and DHCP (`SRV-DHCP`) Servers

---

## 🛡️ Core Security Features Implemented
### 1. Layer 3 Micro-Segmentation
Extended Named ACLs are applied strictly in the **inbound** direction on Switch Virtual Interfaces (SVIs) to explicitly permit required traffic and drop all unauthorized inter-VLAN requests.

### 2. Layer 2 Threat Mitigation
* **DHCP Snooping:** Access ports are set as "untrusted" to drop fake `DHCPOFFER` packets from rogue servers. Core uplinks are trusted.
* **Dynamic ARP Inspection (DAI):** Utilizes the DHCP binding database to intercept and destroy malicious ARP spoofing/Man-in-the-Middle attempts.
* **Port Security:** MAC address limit set to `maximum 2` with violation mode `restrict` to prevent unauthorized hub extensions.
* **BPDU Guard:** Immediately disables (`err-disable`) any edge port if a rogue unmanaged switch is plugged in.

### 3. Identity & Access Management (802.1X)
Edge switches act as authenticators. Ports remain dead until valid Extensible Authentication Protocol (EAPOL) credentials are provided and validated against the central RADIUS server.

### 4. Perimeter Security
The Cisco ASA Firewall utilizes strict Security Levels (`Inside 100`, `Guest 0`, `Outside 0`) and Dynamic NAT to permanently mask internal corporate IPs from external ISP traffic.

---

## 🚀 How to Run the Simulation

### Prerequisites
* **Cisco Packet Tracer** (Version 8.0 or higher recommended).

### Setup Instructions
1. Clone or download this repository to your local machine.
2. Open the `.pkt` (Packet Tracer) file included in the project folder.
3. Allow the network a few moments to converge (STP calculation). You may click the `Fast Forward Time` button (Alt+R) to speed this up.

---

## 🧪 Testing and Verification Guides

Here is how to test the active security features within the Packet Tracer environment:

### 1. Verify Micro-Segmentation (ACLs)
* Open the **Kitchen PC (VLAN 130)**.
* Attempt to ping the **Finance PC (VLAN 40)**. 
* *Result:* The ping will fail (`Request timed out`). 
* *Validation:* Run `show ip access-lists` on `SW-CORE` to see the drop counters incrementing.

### 2. Verify DHCP Snooping
* Ensure the **Rogue-DHCP** server is active on `SW-B1`.
* Open a **Victim PC**, toggle IP Configuration to `Static`, then back to `DHCP`.
* *Result:* The PC will successfully pull a safe `10.x.x.x` address. The switch drops the rogue `192.168.x.x` offer.
* *Validation:* Run `show ip dhcp snooping statistics` on `SW-B1`.

### 3. Verify Dynamic ARP Inspection (DAI)
* Open the **Attacker PC** and assign it a fake static IP (e.g., `10.70.70.200`).
* Open the Command Prompt and ping the Default Gateway.
* *Result:* The ping fails.
* *Validation:* Run `show ip arp inspection statistics` on `SW-B1` to see the DHCP Drops increment.

### 4. Verify BPDU Guard
* Drag a new standard 2960 switch into the workspace.
* Connect it to any secured employee access port on `SW-B1` (e.g., `Fa0/4`).
* *Result:* The link light instantly turns Red. 
* *Validation:* Run `show interfaces status` on `SW-B1` to see the port explicitly labeled as `err-disabled`.

---

## 🔮 Future Scope
* **Cisco Identity Services Engine (ISE):** Upgrading from standard RADIUS for dynamic VLAN assignments and endpoint posture assessments.
* **Next-Generation Firewall (NGFW):** Replacing the ASA with Cisco Firepower to introduce Deep Packet Inspection (DPI) at the network edge.
* **Infrastructure as Code (IaC):** Utilizing Network Automation (Python/Ansible) to push port security configurations autonomously.

---

## 📚 References
1. **NIST Special Publication 800-207**: Zero Trust Architecture (Rose, S. et al., 2020).
2. **Cisco Systems**: Configuring DHCP Snooping and Dynamic ARP Inspection, Cisco IOS Security Configuration Guide.
3. **IEEE Computer Society**: IEEE Standard for Port-Based Network Access Control (802.1X).