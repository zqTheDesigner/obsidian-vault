[Original Article](https://gist.github.com/DannyQuah/f686c0e43b741468e12515cd79017489)

### iPad preparation
- Download iSH Shell App
- [[Install and set up Git]]

### Initialise iOS Obsidian vault via iSH git repo
- iPad > Obsidian > Create a new vault, exit Obsidian 
- iPad > iSH
```sh
# make mountpoint 
mkdir -p /mnt/zq(user name)/Obsidian

# mount the entire Obsidian
mount -t ios null /mnt/zq/Obsidian 
# File picker appears, select the main Obsidian folder, not the child folder

cd /mnt/zq/Obsidian/{the created obsidian workspace}

# initialize git to the folder 
git init
git remote add origin {the SSH url copied from the acutal Obsidian vault git repo}

# need some complex process to pull the data, cannot recall what exactly I did
git add .
git commit -m "init"
git push

git branch -M main
git pull --rebase
# if there is conflicts, likely because of the default configuration json files from obsidian, use git rebase —skip to skip them. 

git push

```

### Normal work cycle
- iPad > iSH 
	- `mount -t ios null /mnt/zq/Obsidian`
	- Sometimes don't need to mount, if git pull failed. 
	- `cd /mnt/zq/Obsidian` 
	- `git pull`
- Work normally on Obsidian app
- iPad > iSH (mount and cd again), `git push`
- To un-mount: `umount /mnt/zq/Obsidian`

