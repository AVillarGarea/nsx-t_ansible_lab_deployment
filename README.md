# nsx-t_ansible_lab_deployment

# Be aware that 
On "deploy-nsx-v2.2.yml" you need to replace line 127 with the URL of your own repo. This is how it looks like in my playbook: "url: https://YOUR_REPO/nsx-lcp-{{ ubuntu_lcp_build }}-ubuntu-xenial_amd64.tar.gz"

# Playbook overview
This is the playbook I use to have NSX-T deployed and configured automatically in my base lab, with the specific settings that fit my needs. Ansible experts and programmers may find this playbook far from optimal. While they are probably right, my objective was not to build the best possible playbook, just to achieve my automation goals, which I did. I’m sharing my experience in case it helps others.

# Disclaimer
I've tested the playbook with NSX-T 2.2.0. I'm not planning any update, neither for programming improvements nor to add new or modified features of NSX.

# My lab environment includes
 - 1x vCenter Server version 6.5.0
 - 1x Management cluster with two ESXi hosts on version 6.5.0
 - 1x Compute cluster with two ESXi hosts on version 6.5.0
 - 2x KVM nodes running Ubuntu 16.04
 - 5x VMs, 3 on ESXi and 2 on KVM, for the typical 3-tier app tests
 - 1x AD server, also doing DNS and NTP
 - 1x Ubuntu VM with Ansible installed, which has access to all lab components

# The playbook does the following (in order of execution):
 - Deploys NSX appliances (1x NSX Manager, 1x NSX Controller, 2x NSX Edge VMs)
 - Joins Controller with the Manager and creates Controller Cluster ( 1 member only)
 - Installs 3rd party packages on my 2x Ubuntu-based KVM nodes
 - Installs NSX LCP component on the KVM nodes
 - Joins KVM nodes with the Manager as Fabric Nodes
 - Adds vCenter as Compute Manager (to read cluster information)
 - Enables NSX auto-installation for my ESXi compute cluster
 - Creates Transport Zones (overlay and VLAN), one Uplink Profile and on IP Pool
 - Adds KVM nodes as Transport Nodes
 - Adds Edge nodes as Transport Nodes
 - Creates and Edge Cluster and adds both Edge nodes to it
 - Configures Auto-Transport Node creation for my ESXi compute cluster
 - Configures syslog and tunes NSXCLI timeout in all NSX appliances to fit my needs
 - Created overlay and VLAN-backed logical switches
 - Connects the VMs running on the KVM nodes to the right logical switches
 - Updates Quagga router configuration (this is the router NSX interacts with in my lab)
 - Creates and configures T0 and T1 logical routers (including uplinks and downlinks)
 - Configures BGP and route advertisement and redistribution in NSX
 - Creates VM name based NS Groups
 - Configures DFW rules and sections for my 3-tier app using the NS Groups created above, and modifies the default rule so that it drops all traffic
 - Creates DFW rules to allow management access to all the VMs in the environment
 - Configures a load balancer across all my web VMs
 - Configured scheduled backups
 - Accepts NSX Manager EULA and CEIP

# The playbook runs some additional tasks
Required in my lab because I try early versions that are not fully baked and sometimes miss some install/uninstall code. In a production environment these should not be required:
 - Remove known SSH hosts from the VM where I'm running the Ansible playbook
 - Remove VIBs and reboot ESXi hosts
 - Remove NSX packages and reboot KVM nodes
 - Manual installation of LCP on KVM nodes
