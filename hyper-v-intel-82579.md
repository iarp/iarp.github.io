# Hyper-V and the Intel 82579

> Originally Written: 2012-03-02

Hyper-V comes in two flavours. One is a core installation containing bare minimum Windows Server 2008 R2 only running 
Hyper-V service (few others can be installed) and the other is to install Windows Server 2008 R2 full blown copy on the 
machine and add Hyper-V as a role.

If you go with the core-installation route, you don't have to deal with the overhead of a full GUI, i.e. less resources 
required to run the host machine, leaving more open to Virtual Images. On the down side of that, most management of a 
core-installation is through a CLI system, it's run through a powershell.

If you go with a role-installation (full 2008 R2 install) then you get the easier GUI system, but I personally found it 
required more configuring. Since this installation is a full windows server copy you get all it's bonuses plus you can 
run other things such as web servers, AD..etc.

## Ethernet Driver Installation

Depending on which version of Hyper-V you installed the instructions are basically the same. Since I went with the 
core-installation version that is what i will be outlining here.

Yet again I ran into issues with that damn ethernet jack. After lots of Googling and time spent, I've come to learn 
that Intel has purposefully disabled the easy-to-install drivers for this particular ethernet because they know 
customers would try to run servers from a consumer grade motherboard.

The fix is easy enough. [Download these drivers](http://downloadcenter.intel.com/Detail_Desc.aspx?lang=eng&ProductID=3299&ProductFamily=Ethernet+Components&ProductLine=Ethernet+Controllers&ProductProduct=Intel®+82579+Gigabit+Ethernet+Controller&DwnldID=18725)
(not the Itanium-based one) and extract it to a computer and navigate into `/PROWinx64/PRO1000/Winx64/NDIS62/` 
and edit the `e1c62x64.inf` file.

Under the `[ControlFlags]` section you will see a `ExcludeFromSelect = ` followed by 2 more lines, 
remove all 3 lines and only leave `[ControlFlags]`

Then under the [Intel.NTamd64.6.1.1] heading (that 6.1.1 number may change with time and updates) copy all of lines 
that contain E1503NC and paste them under the [Intel.NTamd64.6.1] heading under the lines that are currently there.

Save the folder to a USB stick and attach it to the server (unless you did the changes on the server).

From the console on the server itself 2 console windows will be open, one regular command prompt and another running 
powershell with the management script. Go to the regular command prompt (black one) and you will need to change the 
command prompt to the USB drive. Now, here it's a bit tricky, i never found out how to find drive letters so i guessed 
until i found the right one.

To change the command prompt to the USB drive you basically just try in E: if the drive is E or F: and you will notice 
the prompt now says E:&gt; type in DIR to get a listing of what is on the drive and make sure you're on the proper USB 
stick. Then CD into the directory that contains the e1c62x64.inf files and type in

`pnputil -i -a e1c62x64.inf`

It will install the drivers and it will warn you about unsigned drivers, just accept it and let it install. 
Restart the server and the network card should show up once again.

## Onboard RAID supported

The one thing that worried me most about ESXi was that it did not seem to support any types of onboard/software raids. 
Right off the bat this Hyper-V install worked on the RAID without an issue.

## Service Pack 1

So, we now have a Hyper-V server with a working ethernet connection. My first reaction was to get on via remote from 
my main desktop and start up a test image. Yet again i'm faced with a problem, Unable to initalize.

This problem happens because a particular setting in the BIOS (i've forgotten the name) has been enabled but Windows 
cannot access it or do something with it like it should. The solution is to install SP1.

You're not going to find a Hyper-V SP1-only package out there, you will find a 1.5GB download and that basically 
upgrades the machine but who wants to waste that much bandwidth? The solution here is to 
[grab the Win7/WinServer 2008 R2 SP1 package](http://www.microsoft.com/download/en/details.aspx?id=5842) 
(windows6.1-KB976932-X64.exe or windows6.1-KB976932-X86.exe depending on 64 or 32 bit). 
Because Hyper-V core-installation runs on 2008 R2, that installer will work.

Download it and save it to a USB stick again, then on the server, get back onto the USB drive and CD into the folder 
containing the SP1 installer. Then just type in the files name and hit enter to run it. It may pause for a minute or 
two but it will go and will say "Hyper-V Service Pack 1 Installation" or something along that lines. Let it run and 
restart a few times (mine restarted 3 times)

## Conclusion

Intel is certainly a sneaky bastard by disabling the ability to easily install the drivers. From what I've read online, 
they purposefully did this to stop people from installing server installations onto these desktop/consumer 
grade motherboards.

So far my feeling on Hyper-V isn't good. I'm worried about certain aspects compared to ESXi. First off, and I 
have not done much looking-into to reading on is a way to few general/overall CPU usage, or RAM usage. So far 
I have not found any way of viewing such things on Hyper-V, while ESXi gave you breakdown's of all aspects 
you could imagine.

I'm also worried about the network manager, my dealings so far with 2 connections to far have been ehh at best, 
there doesn't seem to be too much in way of controlling multiple virtual networks on a single physical connection. 
I only see one virtual to physical.

One major benefit i see to using Hyper-V is the backup ability. I never liked how there was no easy access to 
ESXi root to copy the virtual computers to a backup drive, while on Hyper-V they are obtainable through a 
protected semi-hidden share.

Time will tell for the rest. I will upload the drivers with modifications made to this blog post at a later date 
when I have access to them. That way if a link breaks we have a copy. 
[Intel 82579V Fix Source](http://communities.intel.com/message/138286)

## Downloads

* [Intel 82579 x64 eithernet driver modified](https://mega.nz/#F!GoJEWCTa!k1oR683mNrsnvDA7Y5dcDA)
* [coreconfig application](https://mega.nz/#F!GoJEWCTa!k1oR683mNrsnvDA7Y5dcDA)

# Forum Post

I've put this here just in case it gets removed from intel's website.

15. Re: Intel(R) 82579V Gigabit Network device issues

This is actually a dirty trick by Intel; someone has decided they don't want us installing server operating 
systems on desktop motherboards. There is however a very easy fix for this; all Intel have done is add a few 
comments to the driver .inf to stop server operating systems picking up desktop motherboard ethernet controllers. 
First thing to do is download ... which is the full driver set for all Intel ethernet controllers; extract 
this to a handy location. Once done navigate to the PRO1000 folder; depending on whether you have a 32bit or 64bit 
OS you will then want either Win32 or Winx64(not Win64 which is for Itanium). The choice for the next folder will 
depend on your OS; NDIS5x is Server2003 or XP, NDIS61 is Server2008 or Vista, NDIS62 is Server2008R2 or 7 In either 
case once you have chosen the correct folder you need to find the .inf starting e1c ; so for Server2008 64bit it 
will be called e1c60x64.inf The part we need to change is below:

```
; e1c51x64.INF (Intel 64 bit extension Platform Only,
; Windows XP 64 bit extension and Windows Server 2003 64 bit extension)
;
; Intel(R) Gigabit Network connections
;******************************************************************************
;
[Version]
Signature   = "$Windows NT$"
Class       = Net
ClassGUID   = {4d36e972-e325-11ce-bfc1-08002be10318}
Provider    = %Intel%
CatalogFile = e1c51x64.cat
DriverVer   = 05/04/2011,11.12.36.0
[Manufacturer]
%Intel%     = Intel, NTamd64.5.2, NTamd64.5.2.1, NTamd64.6.0
[ControlFlags]
ExcludeFromSelect =  
    PCIVEN_8086&DEV_1502, 
    PCIVEN_8086&DEV_1503
[Intel]
[Intel.NTamd64.5.2]
; DisplayName                   Section        DeviceID
; -----------                   -------        --------
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502&SUBSYS_00011179
[Intel.NTamd64.5.2.1]
; DisplayName                   Section        DeviceID
; -----------                   -------        --------
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502&SUBSYS_00011179
%E1503NC.DeviceDesc%            = E1503,       PCIVEN_8086&DEV_1503
%E1503NC.DeviceDesc%            = E1503,       PCIVEN_8086&DEV_1503&SUBSYS_00011179
```

What needs changing first is the `[ControlFlags]` section, I remove all 3 lines but leave the section header so all it 
should say is `[ControlFlags]` and then go on to the `[Intel]` section. Next look at the `[Intel]` section, 
the `[Intel.NTamd64.5.2]` relates to Server 2008 and the `[Intel.NTamd64.5.2.1]` section to Vista; you can see that 
the E1503 lines are missing from the server section, simply copy/paste those two lines, you should end up with 
and `[Intel]` section looking like:

```
[Intel]
[Intel.NTamd64.5.2]
; DisplayName                   Section        DeviceID
; -----------                   -------        --------
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502&SUBSYS_00011179
%E1503NC.DeviceDesc%            = E1503,       PCIVEN_8086&DEV_1503
%E1503NC.DeviceDesc%            = E1503,       PCIVEN_8086&DEV_1503&SUBSYS_00011179
[Intel.NTamd64.5.2.1]
; DisplayName                   Section        DeviceID
; -----------                   -------        --------
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502
%E1502NC.DeviceDesc%            = E1502,       PCIVEN_8086&DEV_1502&SUBSYS_00011179
%E1503NC.DeviceDesc%            = E1503,       PCIVEN_8086&DEV_1503
%E1503NC.DeviceDesc%            = E1503,       PCIVEN_8086&DEV_1503&SUBSYS_00011179
```

Save the inf file and you should then find the driver will install perfectly. Hope this helps Simon
