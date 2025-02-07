# crfi-nodejs-sdk
A Node.js wallet manager for interacting with `crystaleum-wallet-rpc`. </br>
</br>
Forked from PsychicCat/monero-nodejs || For more information about Monero, visit: https://getmonero.org </br>
Maintained for Electronero with upstream and custom patches || For more information about Electronero, visit: https://t.me/electronero </br>
</br>

If you found this useful, please consider [contributing](https://github.com/electronero) to Electronero Network Project!
________________________________

## Install the package

### Clone the Github repository 

``` 
git clone https://github.com/crystaleum/crfi-nodejs-sdk.git
```

### Via Submodule

``` 
git submodule add https://github.com/crystaleum/crfi-nodejs-sdk.git 
```

## Initializing a wallet

Require the module:

``` 
var crfiWallet = require('crfi-nodejs-sdk');
```

Create a new instance of the wallet:

``` 
var Wallet = new crfiWallet();
```

This creates a wallet using the following simplewallet default RPC settings:

* `hostname` - '127.0.0.1'
* `port` - 12345

To connect to a wallet with different settings, pass in the values:

``` 
var Wallet = new crfiWallet($HOSTNAME, $PORT);
```

**Note: versions of crfi-nodejs-sdk prior to 1.0 require `hostname` with the 'http://' prefix, 3.0 and greater only require the IP address.**

## Testing

Some basic tests can now be run locally to verify the library and your simplewallet instance are communicating. The tests assume simplewallet will be listening at the default config settings. Tests are run via mocha.
To run the tests, clone the repository and then:

```
npm install
npm test
```

## Example Usage</br>

``` 
    Wallet.balance().then(function(balance) {
        console.log(balance);
    });
```

## Wallet Methods

### create_wallet
</br>
Parameters:
* `filename` - filename of wallet to create (_string_)</br>
* `password` - wallet password (_string_)</br>
* `language` - language to use for mnemonic phrase (_string_)</br>
Function: Creates a new wallet. </br>
Usage:</br>

``` 
Wallet.create_wallet('crfi_wallet', '', 'English');
```

</br>
### open_walllet
</br>
Parameters:</br>
* `filename` - filename of wallet to open (_string_) </br>
* `password` -wallet password (_string_)</br>
Function: Opens a wallet. </br>
Usage:</br>

```
Wallet.open_wallet('nero_wallet', '');
```

</br>

### balance
</br>
Function: Responds with the current balance and unlocked (spendable) balance of the wallet in atomic units. Divide by 1e12 to convert.</br>
Usage:

``` 
Wallet.balance();
```

</br>
Example response:</br>

``` 
{ balance: 3611980142579999, unlocked_balance: 3611980142579999 }
```

</br>

### address
</br>
Function: Responds with the Monero address of the wallet.</br>
Usage:</br>

``` 
Wallet.address();
```

</br>
Example response:</br>

``` 
{ address: '44AFFq5kSiGBoZ4NMDwYtN18obc8AemS33DBLWs3H7otXft3XjrpDtQGv7SqSsaBYBb98uNbr2VBBEt7f2wfn3RVGQBEP3A' }
```

</br>

### transfer
</br>
Parameters:</br>
* `destinations` - an object OR an array of objects in the following format: `{amount: (*number*), address: (*string*)}` </br>
* `options` - an object with the following properties (_optional_) </br>
</br>

```
{
  mixin: (_number_), // amount of existing transaction outputs to mix yours with (default is 4)
  unlockTime: (_number_), // number of blocks before tx is spendable (default is 0)
  pid: (_string_) // optional payment ID (a 64 character hexadecimal string used for identifying the sender of a payment)
  payment*id: (\_string*) // optional payment ID (a 64 character hexadecimal string used for identifying the sender of a payment)
  do*not_relay: (\_boolean*) // optional boolean used to indicate whether a transaction should be relayed or not
  priority: (_integer_) // optional transaction priority
  get*tx_hex: (\_boolean*) // optional boolean used to indicate that the transaction should be returned as hex string after sending
  get*tx_key: (\_boolean*) // optional boolean used to indicate that the transaction key should be returned after sending
}
```

</br>
Function: Transfers Monero to a single recipient OR a group of recipients in a single transaction. Responds with the transaction hash of the payment.</br>
Usage:</br>

``` 
Wallet.transfer(destinations, options);
```

</br>

Example response:</br>

``` 
{ tx_hash: '<b9272a68b0f242769baa1ac2f723b826a7efdc5ba0c71a2feff4f292967936d8>', tx_key: '' }
```

</br>

### transferSplit
</br>
Function: Same as `transfer`, but can split into more than one transaction if necessary. Responds with a list of transaction hashes.</br>

Additional property available for the `options` parameter:</br>
* `new_algorithm` - `true` to use the new transaction construction algorithm. defaults to `false`. (_boolean_)</br>

Usage:</br>

``` 
Wallet.transferSplit(destinations, options);
```

</br>
Example response:</br>

``` 
{ tx_hash_list: [ '<f17fb226ebfdf784a0f5814e1c5bb78c19ea26930a0d706c9dc1085a250ceb37>' ] }
```

</br>

### sweep_dust
</br>
Function: Sends all dust outputs back to the wallet, to make funds easier to spend and mix. Responds with a list of the corresponding transaction hashes.</br>
Usage:</br>

``` 
Wallet.sweep_dust();
```

</br>
Example response:</br>

``` 
{ tx_hash_list: [ '<75c666fc96120a643321a5e76c0376b40761582ee40cc4917e8d1379a2c8ad9f>' ] }
```

</br>

### sweep_all
</br>
Function: Sends all spendable outputs to the specified address. Responds with a list of the corresponding transaction hashes.</br>
Usage:</br>

``` 
Wallet.sweep_all('44AFFq5kSiGBoZ4NMDwYtN18obc8AemS33DBLWs3H7otXft3XjrpDtQGv7SqSsaBYBb98uNbr2VBBEt7f2wfn3RVGQBEP3A');
```

</br>

Example response:</br>

``` 
{ tx_hash_list: [ '<75c666fc96120a643321a5e76c0376b40761582ee40cc4917e8d1379a2c8ad9f>' ] }
```

</br>

### getPayments
</br>
Parameters:</br>
* `paymentID` - the payment ID to scan wallet for included transactions (_string_)</br>
Function: Returns a list of incoming payments using a given payment ID. </br></br>
Usage:

``` 
Wallet.getPayments(paymentID);
```

</br>

### getBulkPayments
</br>
Parameters:</br>
* `paymentIDs` - the payment ID or list of IDs to scan wallet for (_array_)</br>
* `minHeight` - the minimum block height to begin scanning from (example: 800000) (_number_)</br>
Function: Returns a list of incoming payments using a single payment ID or a list of payment IDs from a given height.</br>
Usage:</br>

``` 
Wallet.getBulkPayments(paymentIDs, minHeight);
```

</br>

### incomingTransfers
</br>
Parameters:</br>
* `type` - accepts `"all"`: all the transfers, `"available"`: only transfers that are not yet spent, or `"unavailable"`: only transfers which have been spent (_string_)</br>
Function: Returns a list of incoming transfers to the wallet.</br>
Usage:</br>

``` 
Wallet.incomingTransfers(type);
```

</br>

### queryKey
</br>
Parameters:</br>
* `type` - accepts `"mnemonic"`: the mnemonic seed for restoring the wallet, or `"view_key"`: the wallet's view key (_string_)</br>
Function: Returns the wallet's spend key (mnemonic seed) or view private key.</br></br>
Usage:</br>

``` 
Wallet.queryKey(type);
```

</br>

### integratedAddress
</br>
Parameters:</br>
* `paymentID` - a 64 character hex string. if not provided, a random payment ID is generated. (_string_, optional)</br>
Function: Make and return a new integrated address from your wallet address and a payment ID.</br>
Usage:</br>

``` 
Wallet.integratedAddress(paymentID);
```

</br>
OR:</br>

``` 
Wallet.integratedAddress();
```

</br>
Example response:</br>

``` 
{ integrated_address: '4HCSju123guax69cVdqVP5APVLkcxxjjXdcP9fJWZdNc5mEpn3fXQY1CFmJDvyUXzj2Fy9XafvUgMbW91ZoqwqmQ96NYBVqEd6JAu9j3gk' }
```
</br>

### splitIntegrated
</br>
Parameters:</br>
* `address` - an integrated Monero address (_string_)</br>
Function: Returns the standard address and payment ID corresponding to a given integrated address.</br>
Usage:</br>

```
Wallet.splitIntegrated(address);
```

</br>
Example response:</br>

```
{ payment_id: '<61eec5ffd3b9cb57>',
  standard_address: '44AFFq5kSiGBoZ4NMDwYtN18obc8AemS33DBLWs3H7otXft3XjrpDtQGv7SqSsaBYBb98uNbr2VBBEt7f2wfn3RVGQBEP3A' }
```

</br>

### height
</br>
Parameters:</br>
* `callback` - a callback function that responds with an error or the response data in the following order: (_error, data_)</br>
Function: Returns the current block height of the daemon.</br>
Usage:

``` 
Wallet.height();
```

</br>
Example response:</br>

``` 
{ height: 874458 }
```

</br>

### stopWallet
</br>
Function: Cleanly shuts down the current simplewallet process.</br>
Usage:</br>

``` 
Wallet.stopWallet();
```

</br>

### store
</br>
Usage:</br>

``` 
Wallet.store();
```

</br>

### rescan
</br>
Usage:</br>

``` 
Wallet.rescan();
```

</br></br>
