Working with GitHub from the command line is more streamlined when using SSH keys for authentication.

This guide assumes you already have a GitHub account setup.

# Getting your SSH Key

Open your public SSH key file with your favourite text editor, by default this is in `$HOME/.ssh/id_rsa.pub` on Linux or MacOS (or WSL2 on Windows), or in `$env\username\.ssh\id_rsa.pub` on Windows directly. You may need to ensure hidden files are visible if you using a GUI text editor.

**Note: ensure you are accessing the `.pub` file, and not your private key file which typically has no extension. If you are using a GUI editor in Windows, and don't explicitly have file extensions turned on, you may see two files called `id_rsa`, in which case the public file will be mistakenly listed as a Microsoft Publisher file.**

The public key is typically a single line (although may appear to be on a few lines if wrap text is enabled), whereas the private key is typically many multiple lines long.

# Adding your SSH Key to GitHub

Copy the entire contents of the file to the clipboard, then open your favourite browser and navigate to [https://github.com/settings/keys](https://github.com/settings/keys).

Click the green `New SSH key` button from the top right-hand side of the screen:
![new ssh key](/images/screenshot-new-ssh-key.png)

You should see a screen that looks like this:
![new ssh key screen](/images/screenshot-new-ssh-key-2.png)

Enter a memorable title, leave the "Key type" as `Authentication Key`, then paste your private key into the "Key" box. Finally, hit the green `Add SSH key` button to finish.

Now when working with GitHub from the command line, you will not be prompted to enter your credentials, for example when pushing to a remote repository.

**Note**: when cloning remote repositories locally, ensure you choose the SSH address, not the HTTPS address. The SSH address will be `git@github.com:user/repo.git` where as the HTTPS address will be `https://github.com/user/repo.git`:
![ssh repo address](/images/screenshot-ssh-repo-address.png)

# Personal access tokens

Now you've added an SSH key, you can not longer authenticate with your password via the command line. If you try, you will be prompted for your credentials, however it will error once you've entered them.

However, you may find yourself need to work with GitHub on another machine, one you don't want to add your private key to, for example, you may need to deploy a private repo to a remote server.

To get around this, you can setup a Personal access token. That way, when you are asked for your credentials at the command line, you can put your username in as usual, but when enter the token in place of the password (you will be prompted for the password but it will error if you enter anything other than your token).

**Note: you will need to clone the repository using the HTTPS address or it will try to find an SSH key/pair** The HTTPS address will be `https://github.com/user/repo.git`
where as SSH address will be `git@github.com:user/repo.git`:
![ssh https address](/images/screenshot-https-repo-address.png)

To set up a Personal access token, open your favourite browser and navigate to [https://github.com/settings/tokens](https://github.com/settings/tokens).

Click the `Generate new token` drop-down box, and select `Generate new token (classic)`:
![generate new token](/images/screenshot-generate-new-access-token.png)

A new screen will appear with quite a few options. Start by adding a note to remind yourself what the token is for. Then set the expiration time you feel comfortable with.

You now need to select the scope of the token, for working with remote packages from a machine without a key/value pair, just select `repo`:

![access token scope](/images/screenshot-access-token-scope.png)
