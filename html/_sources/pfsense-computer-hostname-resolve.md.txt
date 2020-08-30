# pfSense, computers cannot resolve each other hostnames

> Originally Written: 2016-10-06

I've been running the latest pfSense in my home for a couple months now after giving up on consumer based routers. 
Something that has always plauged me was that linux based machines could not see any other computer on the network 
by name alone, you always had to remember the IP address. As well Windows machine could see other Windows machine 
by name but not any linux machines.

Google always brings up people recommending checking DNS settings on the machines or setting up wins on the linux 
computers..etc..etc.

Go make sure these two checkboxes are checked:

* Services
* DNS Resolver
* General Settings page
* Near the bottom make sure these two have a checkbox:
    * DHCP Registration
    * Static DHCP

As long as the computers have the routers ip address for their DNS settings, they will all be able to ping one 
another by name.
