```
# Install open vswitch
sudo apt update && sudo apt upgrade -y
sudo apt install openvswitch-switch -y

# Checking out L2/L3:
ip link
ip address
ip route

# Add network namespace:
sudo -i
ip netns add red
ip netns add blue

# Display network Namespace
ip netns
ls -l /var/run/netns/

# Delete network namespace:
ip netns del (name)

# Execute command on network Namespace
# ip netns exec <namespaceName> {command}
ip netns exec red ip link


# Add a switch on root namespace
# Bridge commands:
#   add-br BRIDGE               create a new bridge named BRIDGE
#   add-br BRIDGE PARENT VLAN   create new fake BRIDGE in PARENT on VLAN
#   del-br BRIDGE               delete BRIDGE and all of its ports
#   list-br                     print the names of all the bridges
ovs-vsctl add-br OVS1
ovs-vsctl list-br
ovs-vsctl show


# Create a veth pair:
# ip link add (end1 name) type veth peer name (end2 name)
# Create  veth pairs:
ip link add eth0-red type veth peer name veth-red
ip link add eth0-blue type veth peer name veth-blue

#list links
ip link

# Attach eth0-red to Red network Namespace
ip link set eth0-red netns red

# Attach eth0-blue to blue network Namespace
ip link set eth0-blue netns blue

# Check
ip netns exec red ip link 
ip netns exec blue ip link 

# Add red/blue veth to OVS1 bridge
ovs-vsctl add-port OVS1 veth-red
ovs-vsctl add-port OVS1 veth-blue


# list ovs ports
ovs-vsctl  list-ports OVS1

# root@ip-10-11-9-31:~# ovs-vsctl show
# 515663b9-9aed-4243-93e3-766cf7c32c04
#     Bridge "OVS1"
#         Port veth-blue
#             Interface veth-blue
#         Port veth-red
#             Interface veth-red
#         Port "OVS1"
#             Interface "OVS1"
#                 type: internal
#     ovs_version: "2.9.2"

# Turn Up veth
ip link set veth-red up
ip link set veth-blue up



# ip link set dev (intf name) up
# Goto red network namespace
ip netns exec red bash

ip link set dev lo up
ip link set dev eth0-red up

# Assign IP address
ip address add 10.0.0.2/24 dev eth0-red


# Goto blue network namespace
ip netns exec blue bash
ip link set dev lo up
ip link set dev eth0-blue up
ip address add 10.0.0.4/24 dev eth0-blue




```
