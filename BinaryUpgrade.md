## Introduction
This tutorial will provide proper instructions on upgrading the installed version of Lisk. Follow the header that applies for the network the node will connect to.

**Table of Contents**

- [Automated Upgrade](#automated-upgrade)
- [Manual Upgrade](#manual-upgrade)
- [Appendix](#appendix)

## Automated Upgrade

### 1. Upgrade Lisk

Copy and paste the following commands into your terminal of choice on the node to be upgraded. Follow the section that applies for the network the node will connect to.

**Mainnet**

```text
cd ~
rm -f installLisk.sh
wget https://downloads.lisk.io/lisk/main/installLisk.sh
bash installLisk.sh upgrade -r main
```

 * You will be prompted for your installation directory, pressing enter will choose the default.

**Testnet**

```text
cd ~
rm -f installLisk.sh
wget https://downloads.lisk.io/lisk/test/installLisk.sh
bash installLisk.sh upgrade -r test
```
 * You will be prompted for your installation directory, pressing enter will choose the default.


### 2. You're done!

## Manual Upgrade

### 1. Switch to your Lisk folder and stop Lisk processes

**Mainnet**

```text
cd ~
cd lisk-main
bash lisk.sh stop
```

**Testnet**
```text
cd ~
cd lisk-test
bash lisk.sh stop
```

### 2. Backup your SSL folder (if needed)

```text
mkdir ~/backup
cp -f ./ssl/* ~/backup/
cp -f ./config.json ~/backup/
```

### 3. Remove your old Lisk folder and install script

**Mainnet**
```text
cd ~
rm -rf ~/lisk-main
rm -rf installLisk.sh
```

**Testnet**
```text
cd ~
rm -rf ~/lisk-test
rm -rf installLisk.sh
```

### 4. Follow the Binary Install Guide!

https://lisk.io/documentation?i=lisk-docs/BinaryInstall


## Appendix

### 1. If you are upgrading from a release prior to 0.2.1 run these commands

```text
sudo apt-get --purge remove postgresql postgresql postgresql-client postgresql-client postgresql-client-common postgresql-common postgresql-contrib postgresql-contrib

rm -rf /var/lib/postgresql/
sudo rm -rf /var/log/postgresql/
sudo rm -rf /etc/postgresql/
```
