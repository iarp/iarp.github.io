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
