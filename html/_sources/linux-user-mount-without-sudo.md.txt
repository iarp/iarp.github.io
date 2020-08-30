# Allow specific username access to mount and umount on linux without sudo

> Originally Written: 2016-06-16

At some point in time I had a couple machines that had cronjobs running as a non-root username that would ensure a 
mounted directory exists and was working. I don't remember why I didn't use root at the time. Since mount and umount 
require sudo access, the cronjob would always fail.

This will allow a specific username access to mount and umount without the need to use sudo.

First open the file using visudo (you must use visudo).

```bash
sudo visudo -f /etc/sudoers.d/myOverrides
```

Then add the following line, replacing MYUSERNAME with your system username.

```bash
MYUSERNAME ALL=(ALL) NOPASSWD:/bin/mount, NOPASSWD:/bin/umount
```
