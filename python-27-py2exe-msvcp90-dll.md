# Python 2.7, Py2Exe and MSVCP90.dll

> Originally Written: 2013-05-07

Once again during a fresh Python 2.7 install and setup on my laptop I ran into an issue while attempting to 
Py2exe compile a project using PyQt4.

This dreaded error i had fought for hours during my initial Python learning stage was

> error: MSVCP90.dll: No such file or directory

After spending yet again, hours trying to fix this today I remembered what I did.

If you currently Google the error above all responses either say "download VC++.... and install it" .. well it's 
already installed on most machines. OR they say "copy the files into the project directory and include it" or it says 
exclude it from the project requirements all together.

Well, i strive to keep my project files as minimal as possible, so copying the file into the project wasn't an option, 
and without the dll the script using the PyQt4 system wouldn't run. And since VC++ is already installed and the issue 
still persists, something else is wrong.

Solution: Search your computer for MSVCP90.dll and copy the damn file into the DLLs folder within your Python 
installation directory. On my computer I copied the file in `C:\Python27\DLLs\`

Problem Solved. No screwing around with extra files or excluding potentially needed files from projects.
