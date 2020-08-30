# ELI5: Computer Fragmentation

> Originally Written: 2013-05-24

I was recently asked how to explain Hard Drive Fragmentation in the simplest form possible. I hope you find it helpful.

Think of the information stored on a hard drive kind of like Lego blocks. You have a giant box of them, 
it's compacted, all blocks are in one giant square but its colours are grouped. There is a process on the computer 
that attempts to keep those group of blocks together as you open, edit and deal with the files. But as time goes on, 
you're playing with files, editing, saving... etc. There become holes in the original giant block. 
The data isn't missing, it's just been placed in a different location on the giant block of Lego.

Now, to keep things simple, lets just say, there is a mapping service that knows the location of each and every 
block in the giant square, it knows that the piece on the top actually belongs to the group of pieces in the middle. 
The problem now begins here, when you first purchase a computer, that giant square of Legos is as a whole, all the 
coloured groups are together and it's easy to find everything without searching the map. But as time goes on, more 
and more pieces end up in other locations, and now the processor on the hard drive needs to view the map more and 
more often, travel to the location, grab the data and then look for the next piece.

This wait time we call seek, the time required for the read/write head in the hard drive to find and get the data. 
What happens after years of usage is those blocks get continually scattered and more and more map viewing is required. 
Eventually, it leads to a slower computer because the hard drive is spending a huge amount of time looking for data 
all over the place.

This entire process is called [Fragmentation](http://en.wikipedia.org/wiki/Fragmentation_(computer)), 
To deal with fragmentation, all operating systems have some type of application that Defragments the data, 
basically it reorganizes the data, physically on the hard drive itself, so that the giant square of Lego is 
as reorganized as possible, grouping colours back together and such.

A typical sign of fragmentation is when a computer takes 10-15minutes to start up and while it's doing 
that, you hear the hard drive just going bananas with clicks and clacks. Those clicks and clacks are the read/write 
head bouncing all over the place looking for the data it needs. Sometimes defragmenting isn't enough, try as you might, 
if it's bad enough, the only possible solution is, copy the personal data off, reformat and start fresh. Quite often 
that freshness can pick a machine back up from the dead.

Obviously fragmentation isn't the only thing that'll slow a computers start time down, someone that has 
19 billion things starting with the computer certainly does not help. All those toolbars, bing, google, yahoo ..etc all 
have hidden things that launch in the background, slowing down the start time even more. But fragmentation 
can easily play a huge role in that.
