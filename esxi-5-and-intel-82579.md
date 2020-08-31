# ESXi 5 and the Intel 82579

> Originally Written: 2012-02-26

## Specs:

* Intel i7-2600k
* ASUS P8Z68-V Pro GEN3 (Bundled with CPU)
* Corsair Dominator DDR3-1600 32GB (4x8GB)
* 2 x Western Digital Velociraptor 300GB 10K hard drives
* Seasonic X760 Gold Power Supply
* Cooler Master CM690 II Advanced case
* Intel Pro/1000GT Ethernet Card
* APC BX1500G UPS

## Problems right off the bat:

* Intel 82579 onboard ethernet nic is too new for the standard ESXi 5.0 installation ISO. It will not install without an HCL listed ethernet card, there is however a work around to insert the drivers needed into the ESXi installer so that it will work. You will find the files listed below.
* the i7-2600k does not support VT-D. The motherboard does support VT so you can run 64-bit images but you cannot use hardware passthrough as it isnot supposed at all. It's my understanding that the i7-2600 does support VT-D (note, no k after the 2600, there is a difference)

<p>Aside from that, I personally do not need the hardware passthrough, but I obvisouly had issues with the ethernet connection. To solve that problem I found a blog post elsewhere that gave the drivers and the converter program. Basically, all you need to do is download the ISO from VMware, download the ethernet driver and the customizer program then use the custom program to insert the drivers into the ISO, reburn that ISO to a new CD and then install from there.</p>

## Useful Code to remember

Since ESXi requires preallocated disks and won't work with Dynamic sized disks, they must be converted. This is easily done through an SSH connection to the server, CD'ing into the folder with the disk and running this command:</p>

`vmkfstools -i original-name.vmdk new-name.vmdk`

## Downloads

* [e1001e - Ethernet Drivers](https://mega.nz/#F!L4Ri1BSR!cDDnTqZWnoMHi_X7mYQXOg)
* [ESXi Customizer v2.7](https://mega.nz/#F!L4Ri1BSR!cDDnTqZWnoMHi_X7mYQXOg)
* [ESXi 5.0 with ASUS P8Z68-V PRO GEN3 Ethernet Drivers](https://mega.nz/#F!L4Ri1BSR!cDDnTqZWnoMHi_X7mYQXOg)
