# Restore Default Linux File Permissions

```bash
find . -type f -exec chmod 664 {} \;
```

# Delete all thumbs.db

```bash
find . -name "Thumbs.db" -print0 | xargs -0 rm

find . -type f -name "Thumbs.db" -exec rmdir {} \;
```

# Replace string in file contents

```bash
# Perl
find ~/location/ -name "*.*"|xargs perl -w -i -p -e "s/What To Find/Replace With What/g"

# SED
find ~/location/ -type f -print0 | xargs -0 sed -e 's/my-silly-home-page-name\.html/index\.html/g' -i

# SED ignoring certain files by name
find ~/location/ -type f -not -path "./.git/*" -print0 | xargs -0 sed -e 's/FindThis/ReplaceWithThis/g' -i
```

# Find string in specific file by name

```bash
grep --include=config.php -rnw . -e 'db_user_name'

find ./wwwroot/ -maxdepth 2 -name 'config.php' -exec grep db_host_name {} \; -print
```

# Repair file line endings

This command is from back in my 1and1 days where line endings would screw things up.

```bash
find . -type f -not -path "./.git/*" -exec dos2unix {} \;
find . -name '*.php' -type f -not -path "./.git/*" -not -path "./vendor/*" -exec dos2unix {} \;
```

# Mirror Windows share

```bash
smbget -Rr smb://username:password@servername/sharename/
```

# Delete empty files

```bash
find /tmp -size  0 -print0 |xargs -0 rm
```
