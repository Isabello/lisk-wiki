**Accounts**

`'get /': 'getAccount'`
`GET /api/v2/accounts/<address>`

This endpoint allows for querying of one specific wallet. Applicable query params are as follows:

publicKey
address
username

Fields can be filtered on all entries, the default returns all data. Examples

Balance only
`GET /api/v2/accounts/12668885769632475474L?fields=balance`

PublicKeys
`GET /api/v2/accounts/12668885769632475474L?fields=publicKey,secondPublicKey`

Returned data is the following:

```
{
  "success": true,
  "account": {
    "address": "Address of account. String",
    "unconfirmedBalance": "Unconfirmed balance of account. String",
    "balance": "Balance of account. String",
    "publicKey": "Public key of account. Hex",
    "unconfirmedSignature": "If account enabled second signature, but it's still not confirmed. Integer",
    "secondSignature": "If account enabled second signature. Integer",
    "secondPublicKey": "Second public key of account. Hex",
    "multisignatures": "Multisignatures. Array",
    "u_multisignatures": "uMultisignatures. Array",
    "delegate": { <--- Only returns of account is a delegate
      "username": "Username. String",
      "address": "Address. String",
      "vote": "Total votes. Integer",
      "producedblocks": "Produced blocks. Integer",
      "missedblocks": "Missed blocks. Integer",
      "rate": "Ranking. Integer",
      "approval": "Approval percentage. Float",
      "productivity": "Productivity percentage. Float"
    },
    "multisignature": { <--- Only returns if the account is a multisig account or a member of a signing group
    "accounts": "array of accounts"
      "accounts": [
        {
          "address": "Multisig account. String",
          "balance": "Multisig account balance. String",
          "multisignatures": [
            "Multisig public key member. String"
          ],
          "multimin": "Min N of sign for a valid tx. Integer",
          "multilifetime": "Lifetime. Integer",
          "multisigaccounts": [
            {
              "address": "Multisig address member. String",
              "publicKey": "Multisig public key member. String",
              "balance": "Multisig balance member. String"
            }
          ]
        }
      ]
    }
  }
}

```

**Blocks**

`'get /': 'getBlocks'` - becomes the standard
`/api/v2/blocks/:<blockid>`

This endpoint returns specific block data as an array. If no block is specified it returns the most recent 20 blocks. Examples

`/api/v2/blocks/7656205841552355056`
`/api/v2/blocks/?limit=10` --- Returns only 10 most recent blocks

Fields can be filtered on all entries, the default returns all data. Examples

Height only
`GET /api/v2/blocks/7656205841552355056?fields=height`

Generator Public Key and reward
`GET /api/v2/blocks/7656205841552355056?fields=generatorPublicKey,reward`

Returned default data is the following:

```
{
    "success": true,
    "block": [{
        "id": "Id of block. String",
        "version": "Version of block. Integer",
        "timestamp": "Timestamp of block. Integer",
        "height": "Height of block. Integer",
        "previousBlock": "Previous block id. String",
        "numberOfTransactions": "Number of transactions. Integer",
        "totalAmount": "Total amount of block. Integer",
        "totalFee": "Total fee of block. Integer",
        "reward": "Reward block. Integer",
        "payloadLength": "Payload length of block. Integer",
        "payloadHash": "Payload hash of block. Integer",
        "generatorPublicKey": "Generator public key. Hex",
        "generatorId": "Generator id. String.",
        "blockSignature": "Block signature. Hex",
        "confirmations": "Block confirmations. Integer",
        "totalForged": "Total block forged. Integer"
    },
    {...} <--- This represents all of the other blocks returned, if applicable
    ]
}
```

**Constants (Or Headers)**

`'get /': 'getConstants'` 
`/api/v2/constants/`

This new endpoint will return constants data, such as fees, milestones, nethash etc

Fields can be filtered on all entires, the default returns all data. Examples

Nethash only
`/api/v2/constants/?fields=nethash`

Milestone and block Reward
`/api/v2/constants/?fields=milestone,reward`

```
{
  "success": true,
  "broadhash": "8680efd470d7d98b418bdf3db40e6dfbb9f0239d2d60f49a33b196938c8cc677",
  "epoch": "2016-05-24T17:00:00.000Z",
  "milestone": 0,
  "nethash": "ed14889723f24ecc54871d058d98ce91ff2f973192075c0155ba2b7b70ad2511",
  "reward": 500000000,
  "supply": 10553743000000000,
  "build": "v20:47:27 15/03/2017\n",
  "commit": "",
  "version": "0.7.0"
  "fees": {
    "send": 10000000,
    "vote": 100000000,
    "secondsignature": 500000000,
    "delegate": 2500000000,
    "multisignature": 500000000,
    "dapp": 2500000000
  }
}
```

**Delegates**

`'get /': 'getDelegates'`
`/api/v2/delegates/`

This endpoint only returns a paginated list of delegates for displaying delegates for voting

Fields can be filtered on all entries, the default returns a full array of data with limit 101(or 20?). Examples

Usernames and approval only
`/api/v2/delegates/?fields=username,approval`

Sorting should also be possible

Sort by name
`/api/v2/delegates/?sort=+username`

Sort by approval
`/api/v2/delegates/?sort=-approval`

```
{
    "success": true,
    "delegates": [{
        "username": "Username. String",
        "address": "Address. String",
        "publicKey": "Public key. String",
        "vote": "Total votes. Integer",
        "producedblocks": "Produced blocks. Integer",
        "missedblocks": "Missed blocks. Integer",
        "approval": "Approval percentage. Float",
        "productivity": "Productivity percentage. Float"
    },
    {...}
    ],
    "totalCount": "Integer"
}
```

**Forging**

`'put /': 'forgingToggle'`
`/api/v2/forging`

This new endpoint will be protected by white list and serve as the new mechanism to enable/disable forging on a node.

```
{
  "publicKey": publicKey,
  "timestamp": timestamp, - we need this to generate the unique signature, otherwise signatures could be recycled and used maliciously
  "signature": signature
}
```

This signature will be generated using Lisk-JS and there is a 10 second difference between generate and node acceptance allowed. This means a user has 10 seconds to submit their timestamp and signature to the node to toggle forging.
This gap is important to remove the ability to reuse signed messages maliciously.

**Peers**

`'get /': 'getPeers'`
`/api/v2/peers/:ip||limit`

Refactor peers api endpoint to return an array always. Filter criteria to reduce the list. Limit is always max 20 but can use sort criteria

Fields can be filtered on all entries, the default returns a full array of data with limit 20. Examples

Only Ips and Ports
`/api/v2/peers/?fields=ip,ports`

Sort can also be used
`/api/v2/peers/?sort=+state`


```
{
  "success": true,
  "peers": [
    {
      "ip": "84.140.172.40",
      "port": 8010,
      "state": 0,
      "os": null,
      "version": null,
      "dappid": null,
      "broadhash": null,
      "height": null,
      "clock": 1491903891185,
      "updated": 1491903831185
    },
    {...}
  ]
  "totalCount": "Integer"
}
```

**Status**

`'get /': 'nodeStatus'`
`/api/v2/status`

Fields can be filtered on all entries, the default returns all data. Examples

Height and Broadhash
`/api/v2/status/?fields=height,broadhash`

Syncing state
`/api/v2/status/?fields=syncing`


```
{
   "success": true,
   "syncing": "Is blockchain loaded? Boolean: true or false", true should indicate it has fallen behind
   "height": "Total blocks in blockchain. Integer", -- this is local only
   "networkHeight": "Total blocks found on syncing node, 0 if not in sync state"
   "broadhash": "Hash of past 5 blocks used for consensus. String",
   "consensus": "Efficiency (%). Integer"
}
```

**Transactions**

`'get /': 'getTransactions'`
`/api/v2/transactions/:id` - TX committed to the db, returns most recent 25 unless specific filter criteria/sort criteria are applied

`'get /': 'getUnconfirmed'`
`/api/v2/transactions/unconfirmed/` - Returns only unconfirmed transactions, always returns 25 as thats the cap of unconfirmed but processed tx

`'get /': 'getPendingMultisignatures'`
`/api/v2/transactions/multisignatures/`- Returns all pending multisignature transactions, can be filtered with address or publicKey

`'get /': 'getQueued'`
`/api/v2/transactions/queued/` - Returns the queued tx, can be filtered with senderId, recipientId, publicKeys, etc

Filters the queued tx, can be filtered with senderId, recipientId, publicKeys, etc

```
{
  "success": true,
  "transactions": [
    {
      "id": "5449806225917864483",
      "height": 1,
      "blockId": "13658550407518916215",
      "type": 0,
      "timestamp": 0,
      "senderPublicKey": "d121d3abf5425fdc0f161d9ddb32f89b7750b4bdb0bff7d18b191d4b4bafa6d4",
      "senderId": "6566229458323231555L",
      "recipientId": "17033820735302139166L",
      "recipientPublicKey": "e771832d97dd5c0e10675552fce21aeea794ab03c86d7aa03d24057cf06c9d70",
      "amount": 10008298357,
      "fee": 0,
      "signature": "72c9b2aa734ec1b97549718ddf0d4737fd38a7f0fd105ea28486f2d989e9b3e399238d81a93aa45c27309d91ce604a5db9d25c9c90a138821f2011bc6636c60a",
      "signatures": [],
      "confirmations": 2559164,
      "asset": {}
    },
    {...}
  ],
  "totalCount": "Integer"
}
```

**Voters**

`'get /': 'getVoters'`
`/api/v2/voters/:<delegate>`

Can be passed: Address, Publickey, username -- needs regex magic

```
{
  "success": true,
  "voters": [
      {
      "address": "Address. String",
      "balance": "Balance of account. String",
      },
      {...}
    ],
  "weight": "Total weight of voters. Integer",
  "totalCount": "Count of voters. Integer"
}
```

**Votes**

`'get/': 'getVotes'`
`/api/v2/votes/:<delegate>`

Can be passed: Address, Publickey, username -- needs regex magic

```
{
  "success": true,
  "voters": [
      {
      "username": "Username of delegate. String",
      "address": "Address. String"
      }
    ],
  "balance": "Total balance used to vote. Integer",
  "totalCount": "Count of votes used. Integer"  
}
```
