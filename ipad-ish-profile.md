alias test='echo test'

alias obsidian-mount='mount -t ios null /mnt/zq/Obsidian;'

alias obsidian-umount='umount /mnt/zq/Obsidian; '


alias obsidian-pull='
cd ~;
cd /mnt/zq/Obsidian/Obsidian-vault;
git pull;
'

alias obsidian-push='
cd ~;
cd /mnt/zq/Obsidian/Obsidian-vault;
git add .;
git commit -m "u";
git push;
'

