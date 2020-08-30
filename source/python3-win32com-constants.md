# Python 3 accessing win32com.client.constants

> Originally Written: 2016-03-01

After installing the relevent [installation of pywin32](https://sourceforge.net/projects/pywin32/) 
you will be able to start accessing win32com items.

I will show you an example for accessing Outlook from Python.

According to Microsofts [documentation here](https://msdn.microsoft.com/en-us/library/office/ff866724.aspx) after 
you've set a variable with the NameSpace object, if you want to get the default folder in outlook for certain things 
like Inbox, Trash, Sent, Deleted...etc. What you do is call **.GetDefaultFolder(olFolderInbox)** and 
that will return the default Inbox folder in the Outlook you are connecting to.

Python doesn't know what olFolderInbox is by default, it's just another variable that is undefined yet. win32com 
gives you access to these constants by going to win32com.client.constants.WhatEverYouNeed. By default though win32com 
does not make these constants, you need to run another application.

* Search your Start Menu in Windows for Pythonwin OR **look for C:\Python34\Lib\site-packages\pythonwin\Pythonwin.exe** replacing Python34 for which ever version you are using.
* Go to **Tools**
* Select **COM Makepy Utility**
* Depending on which application you wish to access, in my case I wanted Outlook, Excel, and Word, you need to look for **Microsoft Outlook XX.X Object Library (X.X)** Word and Excel look similar. You can either repeat steps 2, 3, and 4 for each one individually or just use CTRL and select all 3 at once.
* Select OK
* In the Interactive Window you will see the following line
    * Building definitions from type library...
    * Generation...
    * Importing module
* Once you see Import module hit enter and then close PythonWin application.

You will now be able to access win32com.client.constants.olFolderInbox
