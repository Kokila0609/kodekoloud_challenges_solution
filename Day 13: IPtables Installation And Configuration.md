# Installation of iptables
sudo yum install -y iptables iptables-services

# Allow loopback interface
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow established and related connections.
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Add specific rules as per the task requirements (e.g., blocking a specific port from a specific source):

Example: Block incoming port 6000 except from 172.16.238.14

sudo iptables -A INPUT -p tcp --dport 6000 -s 172.16.238.14 -j ACCEPT

sudo iptables -A INPUT -p tcp --dport 6000 -j DROP

# Save rules for persistence.

sudo service iptables save

sudo systemctl enable iptables

sudo systemctl start iptables

# verify the port connectivity from LB
telnet 172.16.238.14 6000
