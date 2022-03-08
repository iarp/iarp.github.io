# unRAID Redirect container network through another container (VPN tunneling)

Yep! it's easy,

1. Setup a VPN container running (you can use any) & for this example, let's call this container 'vpn'
2. If you want to route the traffic of containerA through the vpn container, then remove the ports exposed on containerA
3. Expose the ports required for containerA on your vpn container.
4. Edit containerA > advanced view > extra parameters: --net=container:vpn (where vpn = your VPN container name)
5. Apply, now containerA traffic should route through the vpn container.

If you're on on 6.8.0-RC1 or any subsequent releases, the above step 4 won't work, to get around that, 
do the following (my preferred method & also works on previous releases)

* Open terminal & run this command: docker network create container:vpn
* Edit containerA and now you should be able to see the vpn network you created in step 4 under 'network type'
* Apply.

[/u/SabreWolF9 comment](https://old.reddit.com/r/unRAID/comments/dkagmn/can_you_put_nzb_behind_a_vpn_easily_on_unraid/f4ca61p/)

# Display live hard drive usage

* Open Nerd Pack and install
    * iotop
    * libffi 

```bash
iotop -oa
```

# Hard Drive Usage by speed

```bash
bwm-ng -T avg -R 1 -i disk -c 1 -I sda,sdb,sdc,sdd,sde,sdf,sdg -H
# Add "-o html" for html output
```

# Find open files by drive

```bash
lsof /dev/md*
```

# Mount and sync another system

```bash
mkdir /mnt/other_documents
mount -t cifs -o user=username,password=password,iocharset=utf8 //192.168.0.2/documents /mnt/other_documents
```

- Add `vers=1.0` to `-o` if connecting to an older unraid.
- `iocharset=utf8` is how file and directory names are handled.

```bash
rsync -avPX /mnt/user/documents/ /mnt/other_documents/
```

Do not forget ending slashes on directories to transfer the contents of source into the destination.

Leaving off trailing slashes causes the source folder itself to sync into the destination path.

```
a - equals rlptgoD below:

r - recursive             recurse into directories
l - links                 copy symlinks as symlinks
p - perms                 preserve permissions
t - times                 preserve modification times
g - group                 preserve group
o - owner                 preserve owner (super-user only)
D -                       same as --devices --specials
    --devices               preserve device files (super-user only)
    --specials              preserve special files

v - verbose               increase verbosity

P -                         same as --partial --progress
    --progress              show progress during transfer
    --partial               keep partially transferred files
    
X - xattrs                preserve extended attributes
W - whole-files         copy files whole (w/o delta-xfer algorithm)
S - sparse              handle sparse files efficiently
                          (VM hdd's and such)
```

# Wireguard config restricts user access to specific internal docker based on IP and Port, while blocking access to unraid webui.

I use a User Script entry with the schedule set to "At First Array Start Only" 
as you do not want to run this more than once per boot.

```
WIREGUARD_INTERFACE=wg1
WIREGUARD_LAN=10.253.2.0/24
MASQUERADE_INTERFACE=eth0

iptables -t nat -I POSTROUTING -o $MASQUERADE_INTERFACE -j MASQUERADE -s $WIREGUARD_LAN


iptables -N WIREGUARD_INPUT
iptables -N WIREGUARD_DROP_WG0_INPUT
iptables -A INPUT -j WIREGUARD_INPUT

iptables -A WIREGUARD_INPUT -i $WIREGUARD_INTERFACE -j WIREGUARD_DROP_WG0_INPUT
iptables -A WIREGUARD_DROP_WG0_INPUT -s 10.253.2.0/24 -j DROP
iptables -A WIREGUARD_DROP_WG0_INPUT -j RETURN 


# Add a WIREGUARD_wg0 chain to the FORWARD chain
CHAIN_NAME="WIREGUARD_$WIREGUARD_INTERFACE"
iptables -N $CHAIN_NAME
iptables -I FORWARD -i $WIREGUARD_INTERFACE -j $CHAIN_NAME

# Accept related or established traffic
iptables -A $CHAIN_NAME -o $WIREGUARD_INTERFACE -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

# These are the docker IP's and ports I want to grant these tunnel wg1 peers access to.
iptables -A $CHAIN_NAME -i $WIREGUARD_INTERFACE -d 172.17.0.10 -p tcp -m tcp --dport 9117 -j ACCEPT
iptables -A $CHAIN_NAME -i $WIREGUARD_INTERFACE -d 172.17.0.20 -p tcp -m tcp --dport 1433 -j ACCEPT
iptables -A $CHAIN_NAME -i $WIREGUARD_INTERFACE -d 172.17.0.4 -p tcp -m tcp --dport 5432 -j ACCEPT

# Drop everything else coming through the Wireguard interface
iptables -A $CHAIN_NAME -i $WIREGUARD_INTERFACE -j DROP

# Return to FORWARD chain
iptables -A $CHAIN_NAME -j RETURN
```