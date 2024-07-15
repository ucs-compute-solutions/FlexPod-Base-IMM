[![published](https://static.production.devnetcloud.com/codeexchange/assets/images/devnet-published.svg)](https://developer.cisco.com/codeexchange/github/repo/ucs-compute-solutions/FlexPod-Base-IMM)
# FlexPod Datacenter Base Configuration using IaC with Cisco IMM and NetApp ONTAP

This repository for FlexPod contains Ansible playbooks to configure Cisco Nexus, NetApp ONTAP, Cisco UCS Intersight, and Cisco MDS. FlexPod Base is a configuration for setting up the base FlexPod components on the network with all port-channels and virtual port-channels configured and only the out-of-band management (OOB-MGMT) VLAN. Tenants for various hypervisors, bare metal OS, and applications can then be configured on top of FlexPod Base. This repository can be used for setting up Cisco devices and NetApp ONTAP Storage as covered in the following Cisco Validated Design (CVD): https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/UCS_CVDs/flexpod_base_imm_m7_iac.html.

The CVD lays out the complete process for configuring the FlexPod using Ansible. Since these playbooks are intended to save time in setting up a working FlexPod, a complete FlexPod as shown below is needed to execute the playbooks. Various simulators could be used to partially test individual playbooks.

![block-diagram](https://github.com/ucs-compute-solutions/FlexPod-Base-IMM/blob/main/ReadmePics/Main-Topology.jpg)  

# Set up the execution environment

To execute various ansible playbooks, a linux based system will need to be setup as described in the CVD with the packages listed at the following pages:

- Cisco UCS Intersight: https://galaxy.ansible.com/ui/repo/published/cisco/intersight/
- Cisco NxOS: https://galaxy.ansible.com/ui/repo/published/cisco/nxos/
- NetApp ONTAP: https://galaxy.ansible.com/ui/repo/published/netapp/ontap/

# How to execute these playbooks?

![block-diagram](https://github.com/ucs-compute-solutions/FlexPod-Base-IMM/blob/main/ReadmePics/Ansible-Order.jpg)

Because a number of manual tasks need to be executed between running the Ansible playbooks, the CVD document should be used as a guide for running the playbooks. Commentary is included in the variable files to guide filling in those values.

In this version of the FlexPod setup, FC boot with FC-NVMe (optional) and NFS is configured by default in the variable files, but iSCSI or M.2 boot and NVMe-TCP (optional) with NFS can also be used.
The steps for setting up a FlexPod with FC boot with FC-NVMe and NFS storage protocols are:

1.  Build an Ansible control VM or bare metal machine as described in the CVD and install the specified Ansible galaxy collections.
2.  Create a directory and clone the repository from Github with "git clone https://github.com/ucs-compute-solutions/FlexPod-Base-IMM.git".
3.  Fill in the variable files according to the CVD.
4.  Follow the manual steps in the CVD to set up the Nexus switches on the network and ssh into each switch.
5.  Execute the Nexus playbook with "ansible-playbook ./Setup_Nexus.yml -i inventory" to setup the Nexus switches.
6.  Follow the manual steps in the CVD to get the NetApp storage cluster on the network.
7.  Execute the NetApp storage playbook with "ansible-playbook -i inventory Setup_ONTAP.yml -t ontap_base_config".
8.  Follow the manual steps in the CVD to get the Cisco UCS Fabric Interconnects (FIs) on the network in Intersight Managed Mode and create an Intersight Account with Licensing.
9.  Claim the FIs into Intersight and setup and deploy the Domain Profile.
10.  Generate the Intersight API Key and associated secret and add to the Ansible variable files.
11.  Execute the IMM playbook to set up the UCS Domain Profile and UCS Chassis Profile with "ansible-playbook ./Setup_UCS_Domain_Chassis_Profiles.yml. 
12.  Follow the manual steps in the CVD to attach the UCS Domain Profile to the UCS Domain and attach all chassis to UCS Chassis Profiles.
13.  If configuring Fibre Channel, follow the manual steps in the CVD to set up the MDS switches on the network and ssh into each switch.
14.  If configuring Fibre Channel, execute the MDS playbook with "ansible-playbook ./Setup_MDS.yml -i inventory".
15.  If desired, install and setup Cisco Assist and NetApp AIQUM on a management pod.
16.  If desired, follow the manual steps in the CVD to claim NetApp AIQUM, Cisco Nexus, and Cisco MDS targets in Cisco Intersight.

The Ansible playbooks and CVD are structured in a way that either a Fibre Channel or IP-only FlexPod can be setup by adjusting the variables. Also, the playbooks can be used to setup the following topology utilizing Cisco Nexus switches that support SAN Switching (93180YC-FX, 93360YC-FX2, or 9336C-FX2-E) for both LAN and SAN switching and 100G FCoE (or optional 32G FC) Uplinks from the FIs to the switches.

![block-diagram](https://github.com/ucs-compute-solutions/FlexPod-Base-IMM/blob/main/ReadmePics/NexusSAN-Topology.jpg)

