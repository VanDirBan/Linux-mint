- **VLAN (Virtual Local Area Network)**
	- **Definition**:
		- A VLAN is a logical subdivision of a Layer 2 network, created to partition and isolate traffic.
		- It groups devices into separate broadcast domains, even if they share the same physical switch infrastructure.
		- Defined by IEEE 802.1Q tagging or port-based assignment.
	- **Why Use VLANs**:
		- **Segmentation**: Limits broadcast traffic to only those devices in the same VLAN.
		- **Security**: Isolates sensitive systems (e.g., management, servers) from general user traffic.
		- **Flexibility**: Allows mixing devices from different departments (e.g., [[Finance]], [[Engineering]]) on the same switch without intermingling traffic.
		- **Scalability**: Simplifies network changes—move a device to another VLAN by changing its port assignment or tag.
	- **Types of VLANs**:
		- **Data VLAN**: Carries user-generated traffic (e.g., PCs, printers).
		- **Voice VLAN**: Prioritizes Voice over IP (VoIP) traffic.
		- **Management VLAN**: Used for network device management (e.g., #ssh access to switches).
		- **Native VLAN**: The untagged VLAN on a trunk link; defaults to VLAN 1 unless changed.
	- **VLAN Tagging (802.1Q)**:
		- **Tagged Frames**: Contain a 4-byte VLAN header indicating the VLAN ID.
		- **Untagged Frames**: Belong to the native VLAN on a trunk port.
		- **Tag Format**:
			- **TPID** (Tag Protocol ID): 0x8100
			- **TCI** (Tag Control Information): 3-bit priority, 1-bit CFI, 12-bit VLAN ID (1–4094).
	- **Port Modes**:
		- **Access Port**:
			- Carries traffic for a single VLAN.
			- Example (Cisco IOS):
			  ```
			  interface GigabitEthernet0/1
			    switchport mode access
			    switchport access vlan 10
			  ```
		- **Trunk Port**:
			- Carries traffic for multiple VLANs (tagged).
			- Example (Cisco IOS):
			  ```
			  interface GigabitEthernet0/2
			    switchport mode trunk
			    switchport trunk allowed vlan 10,20,30
			    switchport trunk native vlan 99
			  ```
		- **Hybrid Port** (on some vendors): Can carry both tagged and untagged VLAN traffic.
	- **Configuring VLANs on Linux**:
		- Install `vlan` package and load 8021q module:
		  ```bash
		  sudo apt-get install vlan
		  sudo modprobe 8021q
		  ```
		- Create VLAN interface:
		  ```bash
		  sudo ip link add link eth0 name eth0.10 type vlan id 10
		  sudo ip addr add 192.168.10.1/24 dev eth0.10
		  sudo ip link set dev eth0.10 up
		  ```
	- **Use Cases**:
		- **Data Center Segmentation**: Separate production, test, and development environments.
		- **Campus Networks**: Isolate student, faculty, and guest networks.
		- **VoIP Deployments**: Ensure voice quality by separating and prioritizing VoIP traffic.
		- **Multi-Tenant Environments**: Provide logical isolation for different customers or departments.
	- **Best Practices**:
		- **Use Consistent VLAN IDs**: Maintain a VLAN ID plan and document it.
		- **Avoid VLAN 1**: Change the native VLAN away from VLAN 1 for security.
		- **Limit Trunk Ports**: Only enable necessary VLANs on each trunk to reduce attack surface.
		- **Secure Management VLAN**: Restrict access to the management VLAN via ACLs or out-of-band connections.
		- **Monitor VLANs**: Use tools (e.g., [[NetFlow]], SNMP) to track VLAN traffic and detect anomalies.
	- **Troubleshooting Tips**:
		- **Show VLAN Status**:
			- Cisco: `show vlan brief`
			- Juniper: `show vlans`
		- **Verify Trunk**:
			- Cisco: `show interfaces trunk`
		- **Check Tagging**:  
		  ```bash
		  sudo tcpdump -i eth0 -e vlan
		  ```
		- **Ping with VLAN Interface**:  
		  ```bash
		  ping -I eth0.10 192.168.10.254
		  ```
	- **References**:
		- IEEE 802.1Q standard
		- Cisco “VLAN Configuration Guide”
		- Linux `vconfig` / `ip link` documentation