# Python 3 accessing Outlook via API/COM

> Originally Written: 2016-03-06

In this example I am connecting to Outlook and pulling all Emails and Contacts from the respective default folders.

```python
import win32com.client

application = win32com.client.Dispatch('Outlook.Application')
namespace = application.GetNamespace('MAPI')

# Obtain all Inbox emails
# https://msdn.microsoft.com/en-us/library/microsoft.office.interop.outlook._mailitem_properties.aspx
inbox_folder = namespace.GetDefaultFolder(win32com.client.constants.olFolderInbox)
for item in inbox_folder.Items:
    print(item.Subject)

# Obtain all Contacts from default Contacts folder
# https://msdn.microsoft.com/en-us/library/microsoft.office.interop.outlook.contactitem_properties.aspx
contacts_folder = namespace.GetDefaultFolder(win32com.client.constants.olFolderContacts)
for contact in contacts_folder.Items:
    print(contact.FirstName)
```
