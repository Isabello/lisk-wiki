Memory Accounts Refactor

## Current Behavior

Currently, `modules/accounts.js` and `logic/account.js` control all functionality related to accounts queries, table creation and entry updates. The main functions exist in logic and include:

**`logic/account.js`**

`Account.prototype.createTables`

Creates memory tables related to accounts:
```
mem_accounts
mem_round
mem_accounts2delegates
mem_accounts2u_delegates
mem_accounts2multisignatures
mem_accounts2u_multisignatures
```

`Account.prototype.removeTables`

Deletes the contents of these tables:

```
mem_round
mem_accounts2delegates
mem_accounts2u_delegates
mem_accounts2multisignatures
mem_accounts2u_multisignatures
```

`Account.prototype.objectNormalize`

Performs validation of accounts related schema 

`Account.prototype.verifyPublicKey`

Performs checks of the public key to ensure its not tampered with.

`Account.prototype.toDB`

Used by `Account.prototype.set` in order to insert or update data

`Account.prototype.get`

Returns accounts related data for specified fields and filter criteria

`Account.prototype.getAll`

Leveraged by the above to gather more results than normally allowed, used for explorer for topAccounts

`Account.prototype.set`

Creates account entries on the mem_accounts table

`Account.prototype.merge`

Updates account entries on the mem_accounts table that already have an entry.
Additionally updates mem_rounds with fees related data

`Account.prototype.remove`

Removes an account from the mem_accounts table.

**`modules/accounts.js`**

The main two functions that need to be looked at for this piece are as follows:

`Accounts.prototype.setAccountAndGet`

Creates the account in the database using `logic.account.set` if it doesn't exist and returns the account data using `logic.account.get`

`mergeAccountAndGet`

Updates an account in the database using `logic.account.merge` and eturns the account data using `logic.account.get`

One major thing of note is that there are two path ways for accounts data to be created/updated. The first is with `Accounts.prototype.setAccountAndGet`, the second is with `logic.account.merge`. 

Table definition for mem_accounts:

```
"username" VARCHAR(20),
"isDelegate" SMALLINT DEFAULT 0,
"u_isDelegate" SMALLINT DEFAULT 0,
"secondSignature" SMALLINT DEFAULT 0,
"u_secondSignature" SMALLINT DEFAULT 0,
"u_username" VARCHAR(20),
"address" VARCHAR(22) NOT NULL UNIQUE PRIMARY KEY,
"publicKey" BYTEA,
"secondPublicKey" BYTEA,
"balance" BIGINT DEFAULT 0,
"u_balance" BIGINT DEFAULT 0,
"vote" BIGINT DEFAULT 0,
"rate" BIGINT DEFAULT 0,
"delegates" TEXT,
"u_delegates" TEXT,
"multisignatures" TEXT,
"u_multisignatures" TEXT,
"multimin" BIGINT DEFAULT 0,
"u_multimin" BIGINT DEFAULT 0,
"multilifetime" BIGINT DEFAULT 0,
"u_multilifetime" BIGINT DEFAULT 0,
"blockId" VARCHAR(20),
"nameexist" SMALLINT DEFAULT 0,
"u_nameexist" SMALLINT DEFAULT 0,
"producedblocks" int DEFAULT 0,
"missedblocks" int DEFAULT 0,
"fees" BIGINT DEFAULT 0,
"rewards" BIGINT DEFAULT 0,
"virgin" SMALLINT DEFAULT 1
```

Mem_Accounts table houses every vital piece of information about an account, including unconfirmed statuses. Therefore, it is updated every time a transaction comes in to keep an accounts state current in the database.

## New Behavior

All accounts related manipulation should be performed inside of PostgreSQL via triggers that operate off of Block insertion and the contained transactions.

Under the new system, `logic/account.js` and `modules/accounts.js` will be stripped of the following functions:

`logic/account.js`

- `Account.prototype.merge`

No longer needed because balances and other fields can be updated with triggers

- `Account.prototype.toDB`

Accounts are no longer created by set

- `Account.prototype.set`

As above

- `Account.prototype.remove`

Accounts will be deleted via triggers

`modules/accounts.js`

`Accounts.prototype.setAccountAndGet`

Underlying functions are going away with triggers, obsoleting this code

`mergeAccountAndGet`

As above

Additionally, the following tables will be removed:

- `mem_accounts2u_delegates`

- `mem_accounts2u_multisignatures`

The following columns will be removed from mem_accounts relating to uncofirmed states:

```
"u_isDelegate" SMALLINT DEFAULT 0,
"u_secondSignature" SMALLINT DEFAULT 0,
"u_username" VARCHAR(20),
"u_delegates" TEXT,
"u_multisignatures" TEXT,
"u_multimin" BIGINT DEFAULT 0,
"u_multilifetime" BIGINT DEFAULT 0,
"u_nameexist" SMALLINT DEFAULT 0,
```

Foreign Key relations that need to be removed from mem_accounts:

```
 "mem_accounts2u_delegates_accountId_fkey"
 "mem_accounts2u_multisignatures_accountId_fkey" 
```

Since in the current model, the database is updated with every transaction, a new method to maintain unconfirmed state.

Proposal for unconfirmed states:

A new type of object will need to be kept in memory and account state cached within it. When a transaction is received by a node, it will check if the account is already found in memory, if so it will update the in memory balance, username, secondsignature and multisig related status.

If the account is not found in memory, it will be loaded into memory via `Accounts.protoype.get` and kept in memory until a block with the transaction is confirmed. The account will be kept in memory after this event for 101 blocks (subject to change).

Further persistence of this data could be gained by caching in something like Redis, if so desired.

Proposal to split out delegates specific data:

Columns to remove from mem_accounts:

```
"username" VARCHAR(20),
"vote" BIGINT DEFAULT 0,
"rate" BIGINT DEFAULT 0,
"delegates" TEXT,
"u_delegates" TEXT,
"nameexist" SMALLINT DEFAULT 0,
"u_nameexist" SMALLINT DEFAULT 0,
"producedblocks" int DEFAULT 0,
"missedblocks" int DEFAULT 0,
"fees" BIGINT DEFAULT 0,
"rewards" BIGINT DEFAULT 0,
```

New tables (from https://github.com/LiskHQ/lisk/issues/543)

**NEW:** `mem_delegates` - This table will store all relevant delegate information and relieve `mem_accounts` of some expensive indexing operations.

- `publickey` - primary/foreign key relation to `mem_accounts`
- `username`
- `fees`
- `rewards`
- `votes`
- `u_votes` (possibly)
- `producedblocks`
- `missedblocks`

This new table will serve as a faster way to store delegates related data without needing to update the mem_accounts data for delegates related items.
