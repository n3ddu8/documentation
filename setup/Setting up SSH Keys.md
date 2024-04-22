SSH stands for Secure Shell. It's a cryptographic network protocol used to establish a secure connection between a client and a server, typically over an unsecured network like the internet. SSH provides a secure channel over which you can remotely access and control devices or transfer files securely. SSH uses various authentication methods to verify the identity of the client and the server, while users can authenticate using passwords, this method is deemed unsecure. The most common method involves the use of cryptographic keys.

Key-based authentication involves generating a pair of cryptographic keys — a public key and a private key. The public key is placed on the server, while the private key is kept secret on the client. When the client attempts to connect to the server, it uses its private key to prove its identity, and the server verifies this using the corresponding public key. This method eliminates the need to transmit passwords over the network.

This document assumes you are using a Linux distribution (directly, or via the WSL2 on Windows) or MacOS. It is possible to use SSH on Windows directly but it's beyond the scope of this document.

# Setting up an SSH Key pair

Run this command:
```shell
ssh-keygen
```

You will be prompted to enter a path for the SSH key files, or you can press enter to accept the default which is `$HOME/.ssh/id_rsa` for the private key and `$HOME/.ssh/id_rsa.pub` for the public key.

You will also be asked if you want to set a passphrase for the keys, if you do you will be prompted for this whenever you attempt to use the SSH protocol. If you don't want to set a passphrase press enter to leave it blank.

It is possible to create multiple key-pairs for use with different servers and applications, by specifying different files.

# Deploying your public key to a remote server

**Note**: the remote server must accept connection via password in order to use this method. If it does not, you will either need to access the server some other way, or give your public key to someone who already has access to the remote server. The key needs to be added to the `$HOME/.ssh/authorized_keys` file, after which you will have access via ssh.

**NOTE: NEVER GIVE OUT YOUR PRIVATE KEY UNDER ANY CIRCUMSTANCES**

To deploy your public key to a remote server, if using the default file path, run:
```shell
ssh-copy-id user@remote-server
```
where `user` is the username of a user on the remote server and `remote-server` is the name or IP address of the remote server.

You will be asked if you trust the remote server, replying `yes` will add this to your `known_hosts` file, usually located in `$HOME/.ssh/`.

You will then be asked for the password for the given user on the remote server, this will be the only time you are asked to enter this password, from now on you will use your key-pair to authenticate.

# Connecting to a remote server via SSH

You can now connect to a remote server using SSH, by running the command:
```shell
ssh user@remote-server
```
where `user` is the username of a user on the remote server and `remote-server` is the name or IP address of the remote server.

Note that leaving `user@` off of this command will assume that the remote user has the same username as your local user.

# Copying files via SSH

You can copy files to and from a remote server over SSH using the Secure Copy Protocol.

## Copying files to a remote server
```shell
scp /path/on/local/machine/to/file.txt user@remote-server:/path/on/remote/server/
```
where `user` is the username of a user on the remote server and `remote-server` is the name or IP address of the remote server.

## Copying files from a remote server
```shell
scp user@remote-server:/path/on/remote/server/to/file.txt /path/on/local/machine/
```
where `user` is the username of a user on the remote server and `remote-server` is the name or IP address of the remote server.

## Working with directories
You can also copy directories to and from a remote server, as with copying directories locally, use the recursive flag `-r`, otherwise the commands are the same, for example:
```shell
scp -r /path/on/local/machine/to/directory/ user@remote-server:/path/on/remote/server/
```
where `user` is the username of a user on the remote server and `remote-server` is the name or IP address of the remote server.
