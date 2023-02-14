Reference: [](https://git-lfs.github.com/)[https://git-lfs.github.com/](https://git-lfs.github.com/)

-   Git large file push keep failing
    
	 If large file already been cached, the git push will keep failing. Run the exact command below, replace the file name.
    
    `git filter-branch --index-filter 'git rm --cached --ignore-unmatch client/static/static-version/20171221_221446.psd'`

