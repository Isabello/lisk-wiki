# Installing Lisk (from Binaries)

This tutorial describes how to install the Lisk using pre-built binary packages. It is built under the assumption you are deploying Lisk for the main network.

## 1. Prepare your System

The following operating systems and architectures are supported:

- Linux (x86_64)
- Linux (i686)
- Linux (armv6l)
- Linux (armv7l)
- Darwin (x86_64)
- FreeBSD (amd64)

To complete the installation there are prerequisites that need to be completed. You can find them below if you have not already performed them.

* [Preparing your system for Lisk](https://github.com/LiskHQ/lisk-wiki/wiki/Prerequisites)

If you are looking to upgrade Lisk, please see this document instead.

* [Preparing your system for upgrade](https://github.com/LiskHQ/lisk-wiki/wiki/Binary-Upgrade)

## 2. Download Lisk

Follow the download instructions to install Lisk for your selected platform.

### All Platforms

1. Login to the user you will use to run Lisk. This user was created in the [Prerequisites Guide.](https://github.com/LiskHQ/lisk-wiki/wiki/Prerequisites) If you are already logged in to this user, please skip this step.

  ```text
  su - lisk
  ```

2. Download the installation script.

  ```text
  wget https://downloads.lisk.io/scripts/installLisk.sh
  ```

3. Execute the installation script. This will configure the environment, download and install the Lisk client.

  **Mainnet**
  ```text
  bash installLisk.sh install -r main
  ```   

   You will be prompted for your installation directory, pressing enter will choose the default.

   Once the installation is complete change directory to the new installation directory.

  ```text
  cd lisk-main
  ```

  **Testnet**
  ```text
  bash installLisk.sh install -r test
  ```   
  You will be prompted for your installation directory, pressing enter will choose the default.

  Once the installation is complete change directory to the new installation directory.

  ```text
  cd lisk-test
  ```

4. Accessing Lisk

  To access the Lisk web client, open a browser and navigate to one of the following depending on the network:

  **Mainnet**
  ```text
   http://localhost:8000/
   ```
   **Testnet**
   ```text
   http://localhost:7000/
   ```

   Replace **localhost** with your public IP address if you are not running Lisk on your local machine.

  The Lisk web client should launch successfully.

With all of the above steps complete you are ready to move on to the configuration documentation if you wish to enable forging or SSL. Please see the [Configuration] here for more information.
