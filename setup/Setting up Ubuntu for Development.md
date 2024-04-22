For the purpose of this guide, it is assumed you are using Ubuntu 22.04 LTS. Many of the commands will be the same for newer versions of Ubuntu, as well as for other Debian based distributions. Some commands may also be the same for other distributions, but be aware that the package manager is likely different. Other distributions are outside of the scope of this document.

It is further assumed that you are not logged in as root, and will therefore need to elevate your users privileges by running `sudo`, thus it is also assumed that your user is part of the "sudo" group (or equivalent). Logging in as root is not advisable, but if you are using root, please leave off `sudo` from the following commands. User and group management is outside the scope of this document.

# Updating your Packages & Distribution

Ubuntu uses the "apt" package manager, you can update the list of packages available using the `update` command:
```shell
sudo apt update
```
and install any updates available using the `upgrade` command.
```shell
sudo apt upgrade
```
Adding `dist` will also install any updates for your distribution.
```shell
sudo apt dist-upgrade
```

It is advisable to do this often in your development environment, including on first launch.

You can chain these commands together using `&&`, which will run further given commands on the successful completion of the previous command. You can also add the `-y` flag when upgrading, which will automatically agree to any user prompts:
```shell
sudo apt update && sudo apt dist-upgrade -y
```

# Installing base packages

Depending on how you are running Linux, you may need to install some base packages. Some or all of these may come pre-installed, but if you are using a cloud image for example, it will not come pre-packaged with anything outside of what is required for a stable operating system.

You can install packages using the `install` command. You can install as many packages as you like on one line, with each new package separated by a space.

To get started, install the following:
- build-essentials: basic development tools
- software-properties-common: for managing software repositories.
- ca-certificates: utility for ensuring secure communication over the internet.
- apt-transport-https: 
- openssh: ssh client.
- wget & curl: for downloading files from remote locations.

```shell
sudo apt install build-essentials software-properties-common ca-certificates wget curl
```

# Basic development packages

The following packages will likely be required when developing on Ubuntu:

- git: for source control.
- vim | nano: terminal based text editors, use whichever you prefer.
- python3-pip - the Python package manager (Python comes pre-installed on Linux).
- python3-venv: for creating Python virtual environments - this is packaged separately on Ubuntu but may already be included on other distributions.
- python3-sphinx: documentation generation tool for Python.
- podman: drop-in replacement for Docker, for working with containers.

```shell
sudo apt install git vim python3-pip python3-venv python3-sphinx podman
```

# Optional packages

## VS Code

VS Code is Microsoft's open source text editor that is very popular in the development community. To install this on Linux you need to add the required software repository.

**Note**: if you are using the [WSL2](</setup/The Windows Subsystem for Linux.md>) you should download the [Visual Studio Code installer for Windows](https://go.microsoft.com/fwlink/?LinkID=534107). Once installed, navigate to extensions and ensure the WSL2 extension is installed. For a more detailed guide see [here](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode).

### Import Microsoft's GPG Key
```shell
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
```

### Add Microsoft’s Visual Studio Code Repository
```shell
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
```

### Update you package list
```shell
sudo apt update
```

### Install VS Code
```shell
sudo apt install code
```

### Run VS Code
```shell
code
```

## Docker

If you prefer to use Docker instead of Podman, you can install it from the default Ubuntu repository, but this version will be out-of-date.

### Import Dockers GPG Key
```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### Add Dockers Repository
```shell
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Update you package list
```shell
sudo apt update
```

### Ensure you install from the Docker repo
```shell
apt-cache policy docker-ce
```

### Install Docker
```shell
sudo apt install docker-ce
```

### Add user to Docker group
```shell
sudo usermod -aG docker ${USER}
```
**Note**: the group change will not take effect until you've logged out as your current user.

### Run Docker
```shell
docker run hello-world
```

## Neovim

Neovim is a fork of Vim, the terminal text editor, with some quality-of-life improvements, including support for Lua over vimscript. Like Vim, Neovim is highly extensible and configurable, and can be used to create a very personalised IDE. You can install it from the default Ubuntu repository, but this version will be out-of-date.
 
### Add Neovims Repository
```shell
sudo add-apt-repository ppa:neovim-ppa/stable
```

### Update you package list
```shell
sudo apt update
```

### Ensure you install from the Neovim repo
```shell
apt-cache policy neovim
```

### Install Neovim
```shell
sudo apt install neovim
```

### Run Neovim
```shell
nvim
```

# Working with Microsoft SQL Server

Working with Microsoft SQL Sever requires installing the ODBC driver for SQL Server. At the time of writing version 18 is avaialble, but many Python libraries still only work with version 17, so this is the version we will assume you are installing.

## Installing the ODBC driver

### Import Microsofts GPG Key
```shell
curl https://packages.microsoft.com/keys/microsoft.asc | sudo tee /etc/apt/trusted.gpg.d/microsoft.asc`
```

### Add Microsoft’s SQL Server Repository
```shell
curl https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/prod.list | sudo tee /etc/apt/sources.list.d/mssql-release.list
```

### Update you package list
```shell
sudo apt update
```

### Install the ODBC Driver
```shell
sudo ACCEPT_EULA=Y apt-get install -y msodbcsql17 && sudo apt install unixodbc-dev -y
```

## Installing optional tools

You may also want to install the optioanl SQL Server toolset, for access to the SQL Server command line utility: `sqlcmd`, and the Bulk Copy Program: `bcp` - the latter is particularly useful when working with large volumes of data as writing over the ODBC driver can be quite slow, even with improvements such as the "multi" method.

### Install MSSql Tools
```shell
sudo ACCEPT_EULA=Y apt-get install -y mssql-tools
```

### Add to PATH
```shell
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
```

or if using `zsh`:
```shell
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.zshrc
```

### Reload Run-Commands file
```shell
source ~/.bashrc
```

or if using `zsh`:
```shell
source ~/.zshrc
```
