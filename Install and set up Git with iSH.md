### Install git with iSH
```sh
apk add git
```

### Set up username and email in git
```sh
git config --global user.name "zqthedesigner"
git config --global user.email "zqthedesigner@gmail.com"
```

### Set up ssh with
[Generating new SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
[Adding a new SSH key to GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

```sh
apk add openssh
```

```sh

# ed25519 here is the algorithm for ssh
ssh-keygen -t ed25519 -C "YOUR_EMAIL"
# Then enter the location to store the ssh key

# Adding ssh key to the ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Adding a new SSH key to github account
# Read the public key 
cat ~/.ssh/id_ed25519.pub # This will be same file location with just now the key gen saved ssh key. but added the .pub addribute instead. 

# Follow the documentation, copy the key and add to github page
```