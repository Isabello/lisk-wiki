# Installing Lisk (using Docker)

This tutorial describes how to install Lisk as a Docker based container.

This document is broken into parts. One being Mainnet and the other being Testnet. Please be sure to follow the instructions for only the network you are configuring your machine to use.

## 1. Install Docker

### Windows / Mac OS X

1. Download and install [Docker Toolbox](https://www.docker.com/docker-toolbox) for your operating system.
2. Open the Docker Quickstart Terminal.

#### PuTTY / SSH Access

You can optionally login to your Docker virtual machine, using [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) or any other SSH client.

To do so, please use the following credentials:

* Host: `192.168.99.100`
* Port: `22`
* Login: `docker`
* Password: `tcuser`

### Ubuntu

Log onto your Ubuntu based server and enter the following commands:

**NOTE:** The following is applicable to: **Ubuntu 14.04 (LTS) - x86_64**.

```text
curl -sL https://downloads.lisk.io/scripts/setup_docker.Linux | sudo -E bash -
sudo apt-get install -y docker-engine
```

## 2. Install Lisk

To install the latest version of Lisk as a docker container, please proceed with the following:

Download the appropriate docker image:

**Mainnet:**

```text
docker pull lisk/mainnet
```

**Testnet:**

```text
docker pull lisk/testnet
```

Install the docker image (executed only once per installation):

**Mainnet:**

```text
docker run -d --restart=always -p 0.0.0.0:8000:8000 lisk/mainnet
```

**Testnet:**

```text
docker run -d --restart=always -p 0.0.0.0:7000:7000 lisk/testnet
```

**NOTE:** On Windows or Mac OS X, these commands are issued from within the Docker Quickstart Terminal.

Upon successful completion, you will have a running Lisk node with an up-to-date snapshot of the blockchain. The container is configured to automatically restart upon reboot of the server or any occurrence of an error.

To access the Lisk web client, open: [http://192.168.99.100:8000/](http://192.168.99.100:8000/) if on the mainnet or [http://192.168.99.100:7000/](http://192.168.99.100:7000/) if on a testnet.

The Lisk web client should launch successfully.

## 3. Update Lisk

To update to the latest version of Lisk, simply run the following commands:

**WARNING:** The provided instructions will **remove the previous container** and **create a new one**. Please be sure to backup any data before proceeding. See: `docker cp --help` for more information on how to copy files from the container to the host machine.

Get the docker container id:

```text
docker ps -a
```

Stop the docker container (replace **container_id** with your own id):

```text
docker stop container_id
```

Remove the old container (replace **container_id** with your own id):

```text
docker rm container_id
```

Download the latest docker image:

**Mainnet:**

```text
docker pull lisk/mainnet
```

**Testnet:**

```text
docker pull lisk/testnet
```

Install the docker image (executed only once per installation):

**Mainnet:**

```text
docker run -d --restart=always -p 0.0.0.0:8000:8000 lisk/mainnet
```

**Testnet:**

```text
docker run -d --restart=always -p 0.0.0.0:7000:7000 lisk/testnet
```

Remove any dangling images:

```text
docker rmi $(docker images -q --filter "dangling=true")
```

Your docker image should now be updated.

## 4. Enable Forging

To enable forging for one or more delegates, simply follow the below instructions.

Get the docker container id:

```text
docker ps -a
```

Open a bash prompt on the docker container (replace **container_id** with your own id):

```text
docker exec -it container_id bash
```

Open config.json:

```text
export TERM=xterm; nano config.json
```

Arrow down until you find the following section:

```text
"forging": {
  "secret" : [""]
}
```

Set the secret parameter to your account secret phrase.

```text
"forging": {
  "secret" : ["YourDelegatePassphrase"] <- Replace with your delegate passphrase
}
```

(Optional) In the forging section you will also see an access property, this is used to allow only your IP address to enable forging through the web client.

```text
"access": {
  "whiteList": ["127.0.0.1"] <- Replace with the IP which you will use to access your node
}
```

To set 2 accounts to forge on a single node, enter both account passphrases like below.

```text
"forging": {
  "secret" : ["YourDelegatePassphrase1","YourDelegatePassphrase2"] <- Replace with your delegate passphrases
  "access": {
    "whiteList": ["127.0.0.1"]
  }
}
```

After you have typed in your passphrase. Hit: `Ctrl+ X` Then: `Y`

Exit the docker container:

```text
exit
```

Restart the docker container (replace **container_id** with your own id):

```text
docker restart container_id
```

Then, open the Lisk web client and wait for the blockchain to load. Once the blockchain has loaded, navigate to "Forging" section, and verify that **Forging (Enabled)** appears in the top left corner.

## 5. Enable Secure Sockets Layer (SSL)

**NOTE:** To complete this step you require a signed certificate (from a CA) and a public and private key pair.

Get the docker container id:

```text
docker ps -a
```

Open a bash prompt on the docker container (replace **container_id** with your own id):

```text
docker exec -it container_id bash
```

Open config.json:

```text
export TERM=xterm; nano config.json
```

Arrow down until you find the following section:

```text
"ssl": {
  "enabled": false,         < Change FROM false TO true
  "options": {
    "port": 443,            < Default SSL Port
    "address": "0.0.0.0",   < Change only if you wish to block web access to the node
    "key": "path_to_key",   < Replace FROM path_to_key TO actual path to key file
    "cert": "path_to_cert"  < Replace FROM path_to_cert TO actual path to certificate file
  }
}
```

After you are done, save changes and exit. Hit: `Ctrl+ X` Then: `Y`

Exit the docker container:

```text
exit
```

Stop the docker container (replace **container_id** with your own id):

```text
docker stop container_id
```

Commit a new docker image (replace **container_id** with your own id):

**Mainnet**

```text
docker commit container_id ssl_mainnet
```

**Testnet:**

```text
docker commit container_id ssl_testnet
```

Run the new docker image:

**Mainnet**

```text
docker run -d --restart=always -p 0.0.0.0:8000:8000 0.0.0.0:443:443 ssl_mainnet
```

**Testnet:**

```text
docker run -d --restart=always -p 0.0.0.0:7000:7000 0.0.0.0:443:443 ssl_testnet
```

Open the web client. You should now have an SSL enabled connection.

## 6. Available Commands

Listed below, are the available commands, which can be used to manage your docker container.

To see a list of running docker containers:

```text
docker ps -a
```

To access a bash prompt on a docker container:

```text
docker exec -it container_id bash
```

To monitor the status of a docker container:

```text
docker stats container_id
```

To monitor the log file of a docker container:

```text
docker logs --tail=500 -f container_id
```

To stop/restart/start a docker container:

```text
docker stop container_id
docker restart container_id
docker start container_id
```

To view a full list of available commands:

```text
docker --help
```

For further information on how to install or use Docker, please read the official [Docker Documentation](http://docs.docker.com/).

## 7. Troubleshooting

If you encounter the following error when running `docker` commands:

```text
cannot enable tty mode on non tty input
```

Then please try running each command prefixed with `winpty`. For example: `winpty docker ps -a`.

***

If you encounter the following error when running `docker` commands:

```text
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Then please try running each command prefixed with `sudo`. For example: `sudo docker ps -a`.

If this does not work, please check the Docker daemon is running correctly before proceeding.

***

If you encounter an error while downloading the docker image, using the `docker pull` command.

Please use the following alternative download method:

**Mainnet:**

```text
curl -o lisk-docker.tar.gz https://downloads.lisk.io/lisk/main/lisk-docker.tar.gz
zcat lisk-docker.tar.gz | docker load
```
**Testnet:**

```text
curl -o lisk-docker.tar.gz https://downloads.lisk.io/lisk/test/lisk-docker.tar.gz
zcat lisk-docker.tar.gz | docker load
```

Then proceed with the remainder of the [installation instructions](https://lisk.io/documentation?i=lisk-docs/DockerInstall#2-install-lisk).
