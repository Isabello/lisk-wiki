# Installing Lisk from Source

This tutorial describes how to install Lisk from source on a Ubuntu based machine.

**NOTE:** The following set of instructions applicable to: **Ubuntu 14.04 (LTS) - x86_64**.

## 1. Install Essentials

```text
sudo apt-get update
sudo apt-get install autoconf automake build-essential curl gzip libtool python tar wget
```

## 2. Install PostgreSQL

```text
curl -sL https://downloads.lisk.io/scripts/setup_postgresql.Linux | bash -
```

After it installs, check version of psql:

```text
psql -version
```

Psql should have the following version number (or greater): `9.6.1`

## 3. Configure PostgreSQL

Create a postgresql user (and choose a password):

```text
sudo -u postgres createuser --createdb --password lisk
```

Create a PostgreSQL database and define password for the client:

**Mainnet**

```text
createdb lisk_main
sudo -u postgres psql -d lisk_main -c "alter user "$USER" with password 'password';"
```

**Testnet** (_for development purposes_):

```text
createdb lisk_test
sudo -u postgres psql -d lisk_test -c "alter user "$USER" with password 'password';"
```

## 4. Install Node.js

```text
curl -sL https://deb.nodesource.com/setup_0.12 | sudo -E bash -
sudo apt-get install -y nodejs
```

After it installs, check version of Node.js/npm:

```text
node -v
npm -v
```

- Node.js should have the following version number (or greater): `v0.12.14`
- npm should have the following version number (or greater): `2.15.0`

## 5. Install Lisk

Choose a network and download the appropriate archive:

**Mainnet**

```text
wget https://downloads.lisk.io/lisk/main/lisk-source.tar.gz
```

**Testnet** (_for development purposes_):

```text
wget https://downloads.lisk.io/lisk/test/lisk-source.tar.gz
```

Decompress the archive:

```text
tar -zxvf lisk-source.tar.gz
```

Change directory:

```text
cd lisk-source
```

Install node modules:

```text
npm install --production
```

## 6. Install Lisk Node

This is a specialized version of Node.js used to execute apps within a virtual machine.

Download the Lisk Node archive:

```text
wget https://downloads.lisk.io/lisk-node/lisk-node-Linux-x86_64.tar.gz
```

Decompress the archive:

```text
tar -zxvf lisk-node-Linux-x86_64.tar.gz
```

Check version of Node.js:

```text
nodejs/node -v
```

- Node.js should have the following version number (or greater): `v0.12.14`

## 7. Download Blockchain Snapshot

Download the blockchain snapshot and restore it to the database made previously:

**Mainnet**

```text
wget https://downloads.lisk.io/lisk/main/blockchain.db.gz
gunzip -fcq blockchain.db.gz | psql -q -U "$USER" -d lisk_main
```

**Testnet** (_for development purposes_):

```text
wget https://downloads.lisk.io/lisk/test/blockchain.db.gz
gunzip -fcq blockchain.db.gz | psql -q -U "$USER" -d lisk_test
```

## 8. Install Forever

Install Forever, a Node.js process manager:

```text
sudo npm install -g forever
```

## 9. Start Lisk

Start Lisk:

```text
forever start app.js
```

The Lisk process, logs, and etc can be found with the command:

```text
forever list
```

You will see list of working Node.js processes with logs, process ids and indexes.

Verify that Lisk has started without any errors and synchronized with the db.

After it starts, open: [http://localhost:8000/](http://localhost:8000/) if on the mainnet (once Lisk is launched) or [http://localhost:7000/](http://localhost:7000/) if on a testnet, replacing **localhost** with your public IP address if you have one.

The Lisk web client should launch successfully.

## 10. Enabling Forging

If you are running your node from a local machine, you can enable forging through the web client, without further interruption. **NOTE:** Should the Lisk node or machine need to be restarted, you will need to re-enable forging again.

If your node is running on a remote machine, or if you want to keep forging persistently enabled, you will need to follow the below instructions.

Stop the running Lisk node:

```text
forever stop app.js
```

Open config.json:

```text
nano config.json
```

Arrow down until you find the following section:

```text
"forging": {
    "force": false,
    "secret": [],
    "access": {
      "whiteList": ["127.0.0.1","IP.Address.Goes.Here."] <- Replace with your IP which you will use to access your node
    }
},
```


**(Recommended for local test networks only)** Set the secret parameter to your account secret phrase.

```text
  "secret" : ["YourDelegatePassphrase"] <- Replace with your delegate passphrase
}
```

**(Recommended for local test networks only)** To set 2 accounts to forge on a single node, enter both account passphrases like below.

```text
  "secret" : ["YourDelegatePassphrase1","YourDelegatePassphrase2"] <- Replace with your delegate passphrases
```

After you have made the appropriate changes. Hit: `Ctrl+ X` Then: `Y`

Start Lisk:

```text
forever start app.js
```

Then, open the Lisk web client and wait for the blockchain to load. Once the blockchain has loaded, navigate to "Forging" section, and verify that forging can be enabled with the switch in the top right.

## 11. Enable Secure Sockets Layer (SSL)

**NOTE:** To complete this step you require a signed certificate (from a CA) and a public and private key pair.

Stop the running Lisk node:

```text
forever stop app.js
```

Open config.json:

```text
nano config.json
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

**NOTE:** If SSL Port configured above (ssl > options > port) is within well known ports range (below 1024), you must start Lisk with admin rights:

```text
sudo forever start app.js
```

If the port is above 1023, you can start Lisk normally:

```text
forever start app.js
```

Open the web client. You should now have an SSL enabled connection.

## 12. Configure Autostart using Forever

To have Lisk automatically start each time your machine boots:

1. Install forever-service, a Node.js service manager:

  ```text
  sudo npm install forever-service -g
  ```

2. Change to your Lisk folder and run:

  ```text
  forever-service install lisk
  ```

Your Lisk node should now automatically start when booting your machine. By installing Lisk as a service, it also allows you to manage your node using the following commands:

* Start: ```sudo start lisk```
* Stop:  ```sudo stop lisk```
* Restart:  ```sudo restart lisk```
* Status:  ```sudo status lisk```

## Appendix: Troubleshooting

### Problem 1

  * `bash: /usr/local/bin/forever: No such file or directory`
  * `npm ERR! cb() never called!`
  * `npm ERR! not ok code 0`

It is likely that *forever*, *nodejs* or your *home folder* has been removed.

Please ensure that your old Lisk client isn't running anymore. Then follow the these steps:

  1. Run `sudo apt-get clean`
  2. Run `sudo apt-get update`
  3. Follow the installation guide again (don't just re-install forever!)
  4. Run `sudo npm cache clear`
  5. Run `sudo npm install -g forever`

Your problem should now be resolved.

***

### Problem 2

  * `npm ERR! git clone --template=/root/.npm/_git-remotes/_templates..`
  * `npm ERR! Permission denied (publickey).`
  * Or something else with *github* or *publickey* inside.

It is likely that *git* or *ssh* is not installed and configured.

  1. Run `sudo apt-get install git ssh`
  2. Generate a key pair for ssh `ssh-keygen -t rsa`
  3. Open the just created public key with `nano ~/.ssh/id_rsa.pub`
  4. Create a new SSH key at the GitHub [SSH keys](https://github.com/settings/ssh) settings page.
  5. Copy your public key into the *key* field and click on *Add SSH key*.

Your problem should now be resolved.
