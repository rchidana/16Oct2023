### Configuring Cisco Routers to connect from our Lab Ubuntu system


Append the following to /etc/ansible/hosts file (need sudo rights to do it) <br>

```
# Details of one IOS based router which is ALWAYS ON
[router1]
sandbox-iosxe-latest-1.cisco.com

[router1:vars]
ansible_user=admin
ansible_password=C1sco12345
ansible_connection=network_cli
ansible_network_os=ios
ansible_port=22

# Details of another  IOS based router which is also ALWAYS ON
[router2]
sandbox-iosxe-recomm-1.cisco.com

[router2:vars]
ansible_user=developer
ansible_password=lastorangerestoreball8876
ansible_connection=network_cli
ansible_network_os=ios
ansible_port=22
```

Additionally, in the /etc/ansible/ansible.cfg, add or uncomment the following configurations<br>

```
host_key_checking=False
```

Fire your first ansible adhoc command as follows <br>

```
ansible router1 -m ping -vvvv
ansible router1 -m ios_command -a "commands='show version'"
ansible router1 -m ios_command -a "commands='show clock'"
ansible router1 -m ios_command -a "commands='show interfaces'"
```
