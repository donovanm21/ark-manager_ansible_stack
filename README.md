# Arkmanager Ansible Stack 
This stack will deploy a fully function Ark Survival Evolved Dedicate server. The stack allows for some fine grain tweaking to deploy select maps or all available maps with selected mods per map. 

**This ansible stack is tested on Ubuntu Server LTS 20.04+**

## Stack variable configuration
All the map and server configuration can be found in,

```
groups_vars/gameservers.yml
```

## Selecting Maps
You can uncomment (Remove the # in front of the code) in the above variables yaml file to deploy more maps. The default is to deploy only TheIsland and Ragnarok.

```yaml
# Disabled Map
# - map_name_ark: "TheCenter"
#   map_name: "TheCenter"
#   map_rcon_port: 32346
#   map_ark_port: 7793
#   map_steam_port: 27031
#   map_admin_password: "ADMIN_PASSWORD_HERE"
#   map_max_players: 25
#   map_mods_enabled: "731604991,924619115,1404697612,1260983937,1609138312,2044129379,889745138"
#   cluster_name: "CLUSTER_NAME_HERE"
```

```yaml
#Enabled Map
 - map_name_ark: "TheCenter"
   map_name: "TheCenter"
   map_rcon_port: 32346
   map_ark_port: 7793
   map_steam_port: 27031
   map_admin_password: "ADMIN_PASSWORD_HERE"
   map_max_players: 25
   map_mods_enabled: "731604991,924619115,1404697612,1260983937,1609138312,2044129379,889745138"
   cluster_name: "CLUSTER_NAME_HERE"
```
### Required Configuration:

```yaml
ark_MultiHome: "SERVER_IP_HERE" <-- Change this to the service IP address, for public servers the IP has to be a publically accessable IP for anyone connecting.
map_admin_password: "ADMIN_PASSWORD_HERE" <-- Change this to a secure password with no special characters to use with RCON access.
cluster_name: "CLUSTER_NAME_HERE" <-- Change this to the Cluster name you want to use for all maps on the same server to be clustered togeter.
map_mods_enabled: "731604991,924619115..." <-- Add the mod IDs of mods you want enabled on each map. You can set this per map. 
map_max_players: 25 <-- Change this to how many player slots you want per map.
admins:
	- 55566667777888999 <-- Change this to your steamID to be listed as a admin on the server cluster. You can add multiple admin in the list format.
```

### Default Configuration:
This configuration is the recommended configuration and should remain as is. If you have a need to adjust this feel free but you might run into unexpected issues if you have overlapping configuration between maps.

```yaml
map_name_ark: "TheIsland" <-- Ark map name used in Ansible to identify Ark map "Don't Change"
map_name: "TheIsland" <-- Name used in Ansible to identify the map "Don't Change"
map_rcon_port: 32344 <-- RCON port for map
map_ark_port: 7791 <-- Data port for map
map_steam_port: 27029 <-- Steam query port to find the map in steam servers
```

# Installation and Stack Deploy Steps
## Install git on Linux 

```bash
sudo apt-get install -y git ansible
```

## Clone the git repo

```bash
# Switch to root
sudo su
# Clone the git repo
git clone https://github.com/donovanm21/ark-manager_ansible_stack.git
# Change into directory
cd ark-manager_ansible_stack
```

## Run ansible stack on local Linux server
If you run into an error on the first run, try rerunning the below command a second time. It sometimes fails to set a variable needed in the stack and succeeds on the second run.

```bash
ansible-playbook -i inventory_remote main.yml
```

## Validate Map Deployment
You can confirm that the maps you uncommented has been deployed here as instance files

```bash
sudo ls -la /etc/arkmanager/instances/
```

```bash
# Example Output
4 -rw-r--r-- 1 ark root 2073 Jun 28 20:04 Aberration.cfg
4 -rw-r--r-- 1 ark root 2058 Jun 28 20:04 Genesis.cfg
4 -rw-r--r-- 1 ark root 2510 Jul 15 16:03 instance.cfg.example
4 -rw-r--r-- 1 ark root 2066 Jun 28 20:04 main.cfg
4 -rw-r--r-- 1 ark root 2062 Jun 28 20:04 Ragnarok.cfg
```

## Start a Map
To start a map, be sure to change to the "ark" user that is created as part of the stack and start the map on the server

```bash
# Switch to root
sudo su
# Switch to the ark user
su ark
# Start the Ragnarok map
arkmanager start @Ragnarok
```

## Check Map Status
Run this as the ark user following on from the above command. This will show the current status of the map on the server

```bash
arkmanager status @Ragnarok
```

### References
Some reference links for full guides and how to using the tools in this repo.

Arkmanager Repo & FAQ - [Link](https://github.com/arkmanager/ark-server-tools)
Ansible Basics - [Link](https://docs.ansible.com/ansible/latest/getting_started/index.html)
