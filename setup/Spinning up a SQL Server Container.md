Since SQL Server 2017, Microsoft have made a containerised instance of the Ubuntu based SQL Server available. This is free for development use, but does require a license for use in production. It is assumed you will be working with SQL Server 2022 for the purposes of this guide, but for any other year, simply replace 2022. It is further assumed that you will be working with the latest version available, but for specific versions, see the [dockerhub page](https://hub.docker.com/_/microsoft-mssql-server/).

For the container platform, it is assumed you will be using Podman on Ubuntu, which is easier to install and doesn't require root privileges so is the preferred option for development environments. However, the commands are the same for Docker, simply replace `podman` with `docker`, or `sudo docker` if your user isn't a member of the "docker" group.

# TLDR:

Run the following command to spin up a SQL Server 2022 instance, with host directories mapped as volumes, the timezone set for the UK and the SQL Server Agent Jobs feature enabled:
```shell
podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -e MSSQL_AGENT_ENABLED=True \
   -e 'TZ=Europe/London' \
   -v $HOME/podman-volumes/sql1/data:/var/opt/mssql/data \
   -v $HOME/podman-volumes/sql1/log:/var/opt/mssql/log \
   -v $HOME/podman-volumes/sql1/$HOME:/var/opt/mssql/secrets \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

# Getting the SQL Server image

This step isn't strictly necessary, as when you spin up a container it will check if the relevant image is available, and if not will pull it from the remote repository, however, if you want to pull the image ahead of time you can run:
```shell
podman pull mcr.microsoft.com/mssql/server:2022-latest
```

# Spinning up a default container

By default, a number of decisions are made for you about how your SQL Server instance will be setup, these can be altered when spinning up your container and some of the more common changes will be covered in this document.

To spin up a container using the default options, run:
```shell
podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```
*if this fails, first try removing the `\` and running the whole command on one line*

This command can be broken down as:
|parameter|flag|description|
|---------|----|-----------|
|`-e "ACCEPT_EULA=Y"`|environment|Confirm acceptance of the End-User Licensing Agreement.|
|`-e "SA_PASSWORD=<YourStrong@Passw0rd>"`|environment|Specify password that meets the SQL Server password requirements.|
|`-p 1433:1433`|port|Map a TCP port on the host environment (first value) with a TCP port in the container (second value).|
|`--name sql1`|name|Specify a custom name for the container.|
|`--hostname sql1`|hostname|Explicitly set the container hostname.|
|`-d`|daemon|Run the container in the background.|
|`mcr.microsoft.com/mssql/server:2022-latest`||The SQL Server Linux container image.|

You can ensure your container is running using the command:
```shell
pomdan ps
```
which should return something like:

`68352dbb064c  mcr.microsoft.com/mssql/server:2022-latest  /opt/mssql/bin/sq...  20 minutes ago  Up 20 minutes  0.0.0.0:1433->1433/tcp  sql1`

# Connecting to your SQL Server instance

You should now be able to connect to your SQL Server instance from any data management application. For the server, use `localhost` for most applications, or `[::1]` for Microsoft applications such as SQL Server Management Studio or Azure Data Studio.

By default, you will need to connect using the `sa` user and the password you specified.

# Adding persistence

By default, any changes you make or data you load into your SQL Server instance will not persist should the container be shut-down. This is fine for quick and dirty testing, but if you plan to use this as part of a local development environment then you will need to add persistence.

You can do this in one of two ways, using a volume container, or by mounting a host directory as a volume. Both options have pro's and con's, which are beyond the scope of this document, but for database containers it may be advisable to mount a host directory, as this gives easy access to the database data directly to your file manager.

## Volume container

Create a container volume using the command:
```shell
podman volume create sqlvol
```
where `sqlvol` is replaced with your desired volume name.

Then, when you spin up a container, add the `-v` flag along with your containers name, mapped to the `/var/opt/mssql/` directory inside your container:
```shell
podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -v sqlvol:/var/opt/mssql \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

Specifying a volume that does not yet exist with the `-v` flag will actually create a new empty volume, however it's usually best practice to verbosely create the volume before spinning up your container.

## Mount a host directory

To mount a host directory as a volume, first ensure a suitable directory exists by using the `mkdir` command. For instance, you may want to create a sub-directory specifically for Podman volumes in your home directory, which can be achieved with:
```shell
mkdir -p podman-volumes/sql1
```

This will create the directory `podman-volumes`, with it's own subdirectory `sql1`, which is the name of the container used in this example.

You could map this directly to the `/var/opt/mssql` directory inside the container, as when using a volume container, however it's best practice when mapping to your host to break this down even further.

Create sub-directories for `data`, `log` and `$HOME` by running:
```shell
mkdir podman-volumes/sql1/data && \
mkdir podman-volumes/sql1/log && \
mkdir podman-volumes/sql1/secrets
```

Then, when you spin up a container, add the `-v` flag, along with the appropriate path, for each of the sub-directories you've created:
```shell
podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -v $HOME/podman-volumes/sql1/data:/var/opt/mssql/data \
   -v $HOME/podman-volumes/sql1/log:/var/opt/mssql/log \
   -v $HOME/podman-volumes/sql1/secrets:/var/opt/mssql/secrets \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

# Configuring the Time Zone

By default, the SQL Server instance will be set to `America/Los_Angeles`, to change this you can use the `-e` flag as so:
```shell
podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -e 'TZ=Europe/London' \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

# Enabling Agent Jobs

You may want to be able to develop and test SQL Server Agent Jobs. By default, this feature is disabled, but can be enabled when spinning up a container with the `-e` flag:
```shell
podman run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   -e MSSQL_AGENT_ENABLED=True \
   -d \
   mcr.microsoft.com/mssql/server:2022-latest
```

