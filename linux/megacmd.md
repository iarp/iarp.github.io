# megacmd continuous retry until complete

megacmd will sometimes crash during an upload, this keeps trying, you will need to kill it whenever its done.

```bash
while true; do megacmd sync /home/username/folder-to-upload/ mega:/My Folder/Structure/; done
```
