
# Preparing your Operating System

This tutorial describes how to prepare your operating system for Lisk.

## 1. Confirm your operating system is supported

The following operating systems and architectures are supported:

- [Linux (x86_64)](#linux)
- [Linux (i686)](#linux)
- [Linux (armv6l)](#linux)
- [Linux (armv7l)](#linux)
- [Darwin (x86_64)](#Darwin)
- [FreeBSD (amd64)](#FreeBSD)

If you are unsure which platform to choose from, open a terminal and run the following command:

```text
uname -sm
```

The resulting output, should tell you if your machine is running on a supported operating system and architecture.

If your architecture is not supported yet, you can try building your own packages using the [lisk-build](https://github.com/LiskHQ/lisk-build) automated package building tool.

## 2. Download and Install packages

Follow these instructions to load required software to your system.

Ensure you are logged in as root.

```text
whoami
```


### Linux

#### 1. Install `curl, wget, tar, sudo, unzip, zip`

Execute the following code block in your command line

  ```text
  if [[ -f "/etc/redhat-release" ]]; then
    yum update
    yum install curl wget tar sudo unzip zip
  elif [[ -f "/etc/debian_version" ]]; then
    apt-get update
    apt-get install curl wget tar sudo unzip zip libreadline6 libreadline6-dev
  fi
  ```


#### 2. Create a user to run lisk, create sudo group and give the lisk user sudo permissions

  ```text
  useradd -d /home/lisk -m lisk
  groupadd sudo
  usermod -a -G sudo lisk
  ```

#### 3. Set a password for the lisk user, make this password strong

  ```text
  passwd lisk
  ```

#### 4. Setup the sudoers file

  ```text
  visudo
  ```

  Paste this line in at the bottom of the file
  ```text
  %sudo   ALL=(ALL:ALL) ALL
  ```

On Ubuntu:  Hit: `Ctrl+ X` Then: `Y` to exit and save

On RHEL: type `:wq!`

#### 5. Update the systems Locale

Set the systems operating language to en_US.UTF-8

```text
  if [[ -f "/etc/redhat-release" ]]; then
    localedef -v -c -i en_US -f UTF-8 en_US.UTF-8
    localectl set-locale LANG=en_US.UTF-8
  elif [[ -f "/etc/debian_version" ]]; then
    locale-gen en_US.UTF-8
    update-locale LANG=en_US.UTF-8
  fi
```

#### 6. Reboot your system to apply changes

```text
reboot
```

#### 7. Continue on to installing Lisk!

* [Installing Lisk (from Binaries)](/documentation?i=lisk-docs/BinaryInstall)

### FreeBSD

* Coming soon

### Darwin

* Coming soon
