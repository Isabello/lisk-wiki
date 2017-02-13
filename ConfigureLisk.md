- [Config.json Explained](#config)
- [Forging](#forging)
- [SSL](#secure-sockets-layer)


## 1. Config

```text
{
    "port": 8000,
    "address": "0.0.0.0",
    "version": "0.5.0",
    "minVersion": "~0.5.0",
    "fileLogLevel": "info",
    "logFileName": "logs/lisk.log",
    "consoleLogLevel": "info",
    "trustProxy": false,
    "topAccounts": false,
    "db": {
        "host": "localhost",
        "port": 5432,
        "database": "lisk_main",
        "user": "",
        "password": "password",
        "poolSize": 95,
        "poolIdleTimeout": 30000,
        "reapIntervalMillis": 1000,
        "logEvents": [
            "error"
        ]
    },
    "api": {
        "access": {
            "whiteList": []
        },
        "options": {
            "limits": {
                "max": 0,
                "delayMs": 0,
                "delayAfter": 0,
                "windowMs": 60000
            }
        }
    },
    "peers": {
        "list": [
            {
                "ip": "192.168.1.1",
                "port": 8000
            }
        ],
        "blackList": [],
        "options": {
            "limits": {
                "max": 0,
                "delayMs": 0,
                "delayAfter": 0,
                "windowMs": 60000
            },
            "timeout": 5000
        }
    },
    "broadcasts": {
        "broadcastInterval": 5000,
        "broadcastLimit": 20,
        "parallelLimit": 20,
        "releaseLimit": 25,
        "relayLimit": 2
    },
    "forging": {
        "force": false,
        "secret": [],
        "access": {
            "whiteList": [
                "127.0.0.1"
            ]
        }
    },
    "loading": {
        "verifyOnLoading": false,
        "loadPerIteration": 5000
    },
    "ssl": {
        "enabled": false,
        "options": {
            "port": 443,
            "address": "0.0.0.0",
            "key": "./ssl/lisk.key",
            "cert": "./ssl/lisk.crt"
        }
    },
    "dapp": {
        "masterrequired": true,
        "masterpassword": "",
        "autoexec": []
    },
    "nethash": "ed14889723f24ecc54871d058d98ce91ff2f973192075c0155ba2b7b70ad2511"
}
```


## 1.  Forging

If you are running your node from a local machine, you can enable forging through the web client, without further interruption. **NOTE:** Should the Lisk node or machine need to be restarted, you will need to re-enable forging again.

If your node is running on a remote machine, or if you want to keep forging persistently enabled, you will need to follow the below instructions.

Stop the running Lisk node:

**Binary**
```text
bash lisk.sh stop
```

**Docker**


**Source**
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

Start Lisk:

**Binary/Docker**
```text
bash lisk.sh start
```

**Source**
```text
forever start app.js
```

Then, open the Lisk web client and wait for the blockchain to load. Once the blockchain has loaded, navigate to "Forging" section, and verify that **Forging (Enabled)** appears in the top left corner.

## 2. Secure Sockets Layer (SSL)

**NOTE:** To complete this step require a signed certificate (from a CA) and a public and private key pair.

Stop the running Lisk node:

**Binary/Docker**
```text
bash lisk.sh stop
```

**Source**
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

After you have finished, save changes and exit. Hit: `Ctrl+ X` Then: `Y`

**NOTE:** If SSL Port configured above (ssl > options > port) is within well known ports range (below 1024), you must alter the port specified with setcap or change it to be outside of that range.

```text
sudo setcap cap_net_bind_service=+ep bin/node
```

Once this is done, or the port specified is above 1024, you may start Lisk

**Binary/Docker**
```text
bash lisk.sh start
```

**Source**
```text
forever start app.js
```

Open the web client. You should now have an SSL enabled connection.
