# md5 compare mirrored directories

Build the md5 checklist file

```bash
find -type f -exec md5sum "{}" > /boot/md5_clonezilla.chk
```

Compare the checklist file

```bash
md5sum -c /mnt/user/downloads/md5_backup_clonezilla.chk
```

Log only mismatched md5

```bash
md5sum --quiet -c /mnt/user/downloads/md5_backup_music.chk 2>&1 | tee /mnt/user/downloads/md5_backup_music.chk.log
```
