```
# For linux bridge
ip link add name vnet-brdg-0 type bridge
ip address add 10.0.0.1/24 dev vnet-brdg-0
ip link set dev vnet-brdg-0 up


# Checking out L2/L3:
ip link
ip address
ip route

# Add network namespace:
sudo -i
ip netns add red && ip netns add blue

# Display network Namespace
ip netns
ls -l /var/run/netns/

# Delete network namespace:
ip netns del (name)

# Execute command on network Namespace
# ip netns exec <namespaceName> {command}
ip netns exec red ip link





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

# Add red/blue veth to linux bridge
ip link set dev veth-red master vnet-brdg-0
ip link set dev veth-blue master vnet-brdg-0



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
ip address add 10.0.0.3/24 dev eth0-blue

# to reach other services add ip route on each namespace
ip netns exec red bash
ip route add 172.31.20.0/24 via 10.0.0.1
exit
ip netns exec blue bash
ip route add 172.31.20.0/24 via 10.0.0.1
exit

# Add NAT iptables on root namespace
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -j MASQUERADE
# enable ip forwarding
sysctl -w net.ipv4.ip_forward=1


```
