# Find and replace string within files in an entire directory tree

This will allow you to change a string within all files in an entire directory structure. I had taken on a project
 from a client that had a static website and whomever had created it, using whatever system they decided on, used 
 a really crappy file name structure. Since everything was flat-file, to change a files name and location required 
 editing all other files and updating the menu link.

The following two commands do the same thing, one uses SED and the other PERL. It's your choice.

The backslashes before the .html escape the period character as it typically is a special character 
in regular expressions.

```bash
# PERL:
find ~/location/ -name "*.*"|xargs perl -w -i -p -e "s/What To Find/Replace With What/g"

# SED:
find ~/location/ -type f -print0 | xargs -0 sed -e 's/my-silly-home-page-name\.html/index\.html/g' -i
```

# Find string in directory tree

```bash
find . -type d -name "*whatever*" -print
```

# Find string in specific file by name

```bash
grep --include=config.php -rnw . -e 'db_user_name'

find ./wwwroot/ -maxdepth 2 -name 'config.php' -exec grep db_host_name {} \; -print
```

# Replace space in directory name with underscore 

```bash
find . -exec rename -n 's/ /_/g' '{}' \;

find . -type f -exec rename -n 's/ /_/g' '{}' \;
```

# Replace string in directory name

```bash
find ~/location/ -type f -not -path "./.git/*" -execdir rename 's/FindThis/ReplaceWithThis/' '{}' \;
```

# Restore Default Directory Permissions

```bash
find . -not -path . -type d -exec chmod 755 {} \;
```

# Delete empty directories

```bash
find -depth -type d -empty -exec rmdir {} \;

# rmdir: failed to remove '.': Invalid argument
# ^^ means you ran it on an already empty folder, it cannot remove a folder you're already in.
```

# Directory file count

```bash
find DIR_NAME -type f -print | wc -l
```
