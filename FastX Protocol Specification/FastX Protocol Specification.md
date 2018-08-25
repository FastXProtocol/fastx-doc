## FastX Protocol Specification

### 1. Data Structure

#### Token Deposit

FastX will create UTXO token in child chain after FastX's root chain smart contract received ETH, ERC20 or Erc721 token.

```javascript
struct UTXO {
    address owner;
    address contractAddress; // The smart contract address for the token. If the token is ETH, it will be 0
    uint256 amount; // If the token is ERC721, it will be 0
    uint256 tokenId; // If the token is ERC20, it will be 0
    uint256 blockNumber;
    uint128 txIndex;
    uint128 oIndex;
}
```

#### Transaction

```javascript
struct Transaction {
    uint256 expireTimestamp;
    uint256 salt;
    
    uint256 blockNumber1;
    uint128 txIndex1;
    uint128 oIndex1;
    byte32 sign1; // Need sign all params except sign1, sign2, blknum2, txindex2, oindex2, newowner2
    
    uint256 blockNumber2; // if no input2, it will be 0
    uint128 txIndex2; // if no input2, it will be 0
    uint128 oIndex2; // if no input2, it will be 0
    byte32 sign2; // Need sign all params except sign1, sign2, blknum1, txindex1, oindex1, newowner1
    
    address newOwner1;
    address contractaddress1; // The smart contract address of the new owner1's token. If the token is ETH, it will be zero
    uint256 amount1; // If the token is ERC721, it will be 0
    uint256 tokenid1; // If the token is ERC20, it will be 0
    
    address newOwner2;
    address contractaddress2; // The smart contract address of the new owner2's token. If the token is ETH, it will be zero
    uint256 amount2; // If the token is ERC721, it will be 0
    uint256 tokenid2; // If the token is ERC20, it will be 0
    
	uint256 fee; // transaction fee (FEX)
	
    uint256 blockNumber;
    uint128 txIndex;
    uint128 oIndex;
}
```



### 2. API

FastX provided a JS client for developers.



#### getAllUTXO

##### params

address: `address`, address of the UTXOs

##### return

The Address's all available UTXOs in child chain



#### deposit

##### params

contractAddress: `address`, the smart contract address for the depositing token, 0 if ETH
amount: `uint256`, the depositing amount, 0 if ERC721
tokenid: `uint256`, token id for the asset, 0 if ERC20

##### return

transaction hash



#### sendTransaction

##### params

blknum1: `uint256`, block number of input 1
txindex1: `uint256`, transaction number in that block for input 1
oindex1: `uint256`, output number of that transaction for input 1
blknum2: `uint256`, block number of input 2
txindex2: `uint256`, transaction number in that block for input 2
oindex2: `uint256`, output number of that transaction for input 2
newowner1: `address`, owner of output 1
contractaddress1: `address`, asset address for output 1, 0x0 if ETH
amount1: `uint256`, the amount for the asset, 0 if ERC721
tokenid1: `uint256`, token id for the asset, 0 if ERC20
newowner2: `address`, owner of output 2
contractaddress2: `address`, asset address for output 2, 0x0 if ETH
amount2: `uint256`, the amount for the asset, 0 if ERC721
tokenid2: `uint256`, token id for the asset, 0 if ERC20
fee: `uint256`, transacton fee
expiretimestamp: `uint256`, expiration time for the transaction
salt: `uint256`, salt
address1: `address`, owner of input1
address2: `address`, owner of input2
sign1: `bytes`, owner1's signature
sign2: `bytes`, owner2's signature

##### return

transaction hash



#### startExit

##### params

blknum: `uint256`, block number
txindex: `uint256`, transaction number in that block
oindex: `uint256`, output number of that transaction
contractAddress: `address`, the smart contract address for the exiting token, 0 if ETH
amount: `uint256`, the exiting amount, 0 if ERC721
tokenid: `uint256`, token id for the asset, 0 if ERC20

##### return

transaction hash



### 3. Smart Contract

####Event

##### Deposit

```javascript
event Deposit(
    address depositor
    address contractAddress, // The smart contract address for the token. If the token is , it will be 0
    uint256 amount; // If the token is ERC721, it will be 0
    uint256 tokenId; // If the token is ERC20, it will be 0
	int256 depositBlock;
)
```

##### ExitStarted

```javascript
event ExitStarted(
    address exitor,
    uint256 utxoPos, // UTXO position (blknum * 1000000000 + index * 10000 + oindex)
    address contractAddress, // The smart contract address for the token. If the token is , it will be 0
    uint256 amount; // If the token is ERC721, it will be 0
    uint256 tokenId; // If the token is ERC20, it will be 0
);
```



#### Function

##### deposit

Allows anyone to deposit funds into the Plasma chain

```javascript
function deposit(
    address contractAddress, // The smart contract address for the token. If the token is ETH, it will be 0
    uint256 amount, // If the token is ERC721, it will be 0
    uint256 tokenId, // If the token is ERC20, it will be 0
) public payable;
```



##### startDepositExit

Starts to exit a specified UTXO has no transactions in child chain

```javascript
function startDepositExit(
    uint256 depositPos, // UTXO position (blknum * 1000000000 + index * 10000 + oindex)
    address contractAddress, // The smart contract address for the token. If the token is ETH, it will be 0
    uint256 amount, // If the token is ERC721, it will be 0
    uint256 tokenId, // If the token is ERC20, it will be 0
) public;
```



##### startExit

Starts to exit a specified UTXO has transactions in child chain

```javascript
function startExit(
    uint256 utxoPos, // UTXO position (blknum * 1000000000 + index * 10000 + oindex)
    bytes txBytes, // The transaction being exited in RLP bytes format
    bytes proof, // Proof of the exiting transactions inclusion for the block specified by utxoPos
    bytes sigs // transaction signatures
) public;
```



##### challengeExit

Allows anyone to challenge an exiting transaction by submitting proof of a double spend on the child chain

```javascript
function challengeExit(
    uint256 cUtxoPos, // The position of the challenging UTXO
    uint256 eUtxoIndex, // The output position of the exiting UTXO
    bytes txBytes, // The challenging transaction in bytes RLP form
    bytes proof, // Proof of inclusion for the transaction used to challenge
    bytes sigs, // Signatures for the transaction used to challenge
) public;
```



##### finalizeExits

Loops through the priority queue of exits, settling the ones whose challenge period has ended

```javascript
function finalizeExits() public;
```