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

## GameUserSetting.ini & Game.ini
With the stack you will find some boilerplate configuration that is includes for each map. 

### GameUserSertting.ini for each map
You can edit this configuration and replace it with your own for each map, this will deploy the settings you have set when you deploy the stack automatically for each map.

```yaml
roles/maps/files/ini_files/[MAP_NAME]/GameUserSettings.ini
```

### Game.ini for all map
For this deployment, using the same Game.ini for all maps works the best and so the configuration you add to this file will be used across all maps.

```yaml
roles/maps/files/ini_files/Game.ini
```

## Default Rates
Below is all the current rates and multipliers as part of the default configuration

```
Difficulty 1.0
XP Multiplier 1.0
Taming Speed 2.0
Maturation Time 2.5
Cuddle Time Interval 0.75
Mating Interval 0.25
Egg Hatch 2.5
Harvest Multiplier 2
Base Character Weight 1000
Tribe Size 8
Alliances 4
Tribes per Alliance 4
Max Wild Dino Level 150
Max Tek Dino Level 180
Nanny Imprint 100%
```
# Installation and Stack Deploy Steps
## Install git & ansible on Ubuntu server 

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

## Network Port Validation
Check to see which ports are open on the server for each map. The example output is for a local only test server.

```bash
# This show all the open & listening ports
netstat -tulpn
```

```bash
#Example Output
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 192.168.1.198:32332     0.0.0.0:*               LISTEN      1629734/ShooterGame   <-- Ragnarok RCON Port
tcp        0      0 192.168.1.198:32334     0.0.0.0:*               LISTEN      1629743/ShooterGame
tcp        0      0 192.168.1.198:32336     0.0.0.0:*               LISTEN      1629754/ShooterGame
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      773/systemd-resolve
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      882/sshd: /usr/sbin
tcp        0      0 192.168.1.198:32344     0.0.0.0:*               LISTEN      1629764/ShooterGame
tcp6       0      0 :::22                   :::*                    LISTEN      882/sshd: /usr/sbin
udp        0      0 192.168.1.198:27017     0.0.0.0:*                           1629734/ShooterGame   <-- Ragnarok Steam Query port
udp        0      0 192.168.1.198:27019     0.0.0.0:*                           1629743/ShooterGame
udp        0      0 192.168.1.198:27021     0.0.0.0:*                           1629754/ShooterGame
udp        0      0 192.168.1.198:27029     0.0.0.0:*                           1629764/ShooterGame
udp        0      0 127.0.0.53:53           0.0.0.0:*                           773/systemd-resolve
udp        0      0 192.168.1.198:68        0.0.0.0:*                           771/systemd-network
udp        0      0 192.168.1.198:7779      0.0.0.0:*                           1629734/ShooterGame   <-- Ragnarok Server Data Port
udp        0      0 192.168.1.198:7780      0.0.0.0:*                           1629734/ShooterGame   <-- Ragnarok Server Data Port
udp        0      0 192.168.1.198:7781      0.0.0.0:*                           1629743/ShooterGame
udp        0      0 192.168.1.198:7782      0.0.0.0:*                           1629743/ShooterGame
udp        0      0 192.168.1.198:7783      0.0.0.0:*                           1629754/ShooterGame
udp        0      0 192.168.1.198:7784      0.0.0.0:*                           1629754/ShooterGame
udp        0      0 192.168.1.198:7791      0.0.0.0:*                           1629764/ShooterGame
udp        0      0 192.168.1.198:7792      0.0.0.0:*                           1629764/ShooterGame
```
## Check Map Status
Run this as the ark user following on from the above command. This will show the current status of the map on the server

```bash
arkmanager status @Ragnarok
```

## Helper Scripts
The system role will install a bunch of custom helper scripts that will keep your server up to date and maintenance free. 

- The server will check for ARK updates and mod updates every hour and if an update is available it will grace fully shutdown all maps, install updates and start maps back up. No more waiting for an admin to run update :D
- You will see a crontab install under the root user. This includes a bunch of tasks
	- Daily server restarts at 4:00am (This can be adjusted in the crontab)
	- Dino wipes twice a day (every 12 hours)
	- Genesis2 void glitch fix (daily)
	- Cluster and Map data backups (daily)
	- Proactive player notification via RCON for updates and restarts

These are quality of life additions I personally put together to ensure the highest player experience on all the servers I've managed and deployed. This provided the best balance for me and so feel free to tweak these to your needs and requirements. 

### References
Some reference links for full guides and how to using the tools in this repo.

Arkmanager Repo & FAQ - [Link](https://github.com/arkmanager/ark-server-tools)
Ansible Basics - [Link](https://docs.ansible.com/ansible/latest/getting_started/index.html)
