# [[Git  Setup]]

# Clean Local Branches
#local-branches
``` sh
git branch | grep -v "main" | xargs git branch -D 

git branch | grep -v "dev" | xargs git branch -D 

git branch | grep -v "dev" | grep -v "main" | grep -v "uat" | xargs git branch -D
```


# Pull from new branch in Github
#remote-branches
Pull from a new branch in git that pushed by someone else
[Reference](https://stackoverflow.com/questions/34344034/git-pull-on-a-different-branch)

```sh
# Below code will create a new local branch. and track it to dev branch from the master
# Take note of the ":" here
git fetch origin <branch-name>:<branch-name>

git switch <branch-name>
# Set current branch track the dev branch from origin
# Take not don't need ":" here
git branch --set-upstream-to=origin/<branch-name> <branch-name>

git pull
```

# Rebase
#rebase
```sh
git checkout dev
git pull
git checkout mybranch
// You are at your branch
git rebase dev
solve whatever conflict
# (HEAD is usually the latest, but check the code carefully)
git stage .
git rebase --continue
solve whatever again and repeat.
once it is done
git push -f
need -f because you rebase you change the underlying hash of the root node
```

# Large File
#large-file

Reference: [](https://git-lfs.github.com/)[https://git-lfs.github.com/](https://git-lfs.github.com/)

-   Git large file push keep failing
    
	 If large file already been cached, the git push will keep failing. Run the exact command below, replace the file name.
    
    `git filter-branch --index-filter 'git rm --cached --ignore-unmatch client/static/static-version/20171221_221446.psd'`

