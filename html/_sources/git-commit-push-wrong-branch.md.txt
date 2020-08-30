# Git, committed and pushed to the incorrect branch

> Originally Written: 2013-09-26

I made a mistake today. I made changes while in the `[master]` branch instead of `[develop]`, committed those 
changes and then pushed to the repo.

A relatively easy fix

```
# Ensure you're in the branch that you commited to by accident.
git checkout master

# Reset the branch back one commit.
git reset --soft HEAD^

# Stash the changes
git stash

# Checkout the branch it should be in
git checkout develop

# Apply the stash
git stash apply

# Commit the changes just as you did before, you will need to rewrite the message.
git commit ...

# Push the changes to our develop branch
git push origin develop

# Checkout the original branch
git checkout master

# Force push the commit deletion to the original branch.
git push --force origin master
```
