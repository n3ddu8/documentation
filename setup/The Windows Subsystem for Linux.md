# What is the Windows Subsystem for Linux (WSL2)

According to Microsoft: "The Windows Subsystem for Linux lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dualboot setup."

Think of it as a Linux server, running inside your Windows environment, which you can use to host services, run containers and utalise command-line utilities.

There are a number of popular distributions which are supported out of the box and can be obtained from the Microsoft store. For the purposes of this guide, it is assumed you are installing [Ubuntu](https://en.wikipedia.org/wiki/Ubuntu).

# Prerequisites for Windows 10

If you are using Windows 10, you may need to upgrade from WSL to WSL2, the latest version of the Windows Subsystem for Linux, as well as install some required components. This should not be necessary if you are using Windows 11.

From the start menu, search for Powershell, and choose to `run as administrator`, you will need administrator privileges for this and may be asked to enter a username and password depending on how your system is setup.

To enable WSL, run:
```shell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

To enable the Virtual Machine Platform, run:
```shell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Now you need to set WSL2 as the default instance:
```shell
wsl --set-default-version 2
```

You should now be ready to install a distribution.

# Installing a Distribution

There are two ways to install a distribution, via Powershell or via the Microsoft Store.

## Powershell

From the start menu, search for Powershell, and choose to `run as administrator`, you will need administrator privileges for this and may be asked to enter a username and password depending on how your system is setup.

Now run:
```shell
wsl.exe --install
```
(You can add the argument `-D` followed by the name of the distribution if you want to install something other than Ubuntu.)

## Microsoft Store

From the start menu, search for the Microsoft Store and launch it.

If you are using Windows 10, and you've installed the prerequisites, you can skip this next application, otherwise, search for `Windows Subsystem for Linux`, which should look like:
![wsl](/images/screenshot-wsl.png)
then select it, and click `install`.

Once this has been installed, you can install the distribution of your choice, such as Ubuntu 22.04, which is the latest long-term service (LTS) edition at time of writing. It is advisable to choose the LTS version as in all likelihood, any server you are developing for, will be using an LTS edition. This looks like:
![ubuntu](/images/screenshot-ubuntu.png)
then select it, and click `install`.

## First launch

Whichever method you choose, on first launch you will be prompted to enter some credentials. These are not your Windows credentials, but will be the username and password you use with WSL2 going forward. Note, that it will appear as if nothing is happening when you are writing your password, this is a security measure, rest assured everything you type is being recorded.

# The Windows Terminal

While you can use the terminal program that comes with your chosen distribution, it is recommended that you use the Windows Terminal, as this includes many quality of life features that will improve your day-to-day experience while using WSL2.

If you are using Windows 11, then the Windows Terminal will come pre-installed, and you can simply search for it in the start menu, however, if you are using Windows 10 you will need to install it via the Microsoft Store.

From the start menu, search for the Microsoft Store and launch it. Now search for the Windows Terminal, which looks like this:
![terminal](/images/screenshot-terminal.png)
then select it, and click `install`.
