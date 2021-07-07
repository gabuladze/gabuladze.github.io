---
layout: post
categories: crypto eth erc20
---
_The code snippets in this post use web3.js and assume that you are connected to a geth node._

# Generate Token Interface ABI
ABI stands for Application Binary Interface and is the standard way to interact with contracts in the Ethereum ecosystem, both from outside the blockchain and for contract-to-contract interaction. 

In order to interact with any ERC20 contract, you will need the ERC20 Interface aka IERC20 ABI. To achieve this we can take the following Solidity code:
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.2;
interface IERC20 {
    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint256);
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function allowance(address owner, address spender) external view returns (uint256);
    function transfer(address recipient, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
``` 
And compile it with Solidity compiler by running:
```
$ solc IERC20.sol --abi > IERC20ABI
```

IERC20ABI will hold the JSON ABI of IERC20. The JSON object can be exported with module.exports if you're using Node.js.

# Process ERC20 Token Transactions

The first step would be to determine the token contract address. It can be seen in token’s wiki pages/docs or on [Etherscan Token Tracker](https://etherscan.io/tokens){:target="_blank"}.

## Send ERC20 Token Transactions
In order to send ERC20 token transactions:
1. Initialize the contract instance with JSON ABI & address.
2. Fetch the number of decimals that the token uses.
3. Calculate the native token amount for transfer (amount * 10^decimals).
4. Generate encoded abi string for transfer method with address & native amount as parameters.
6. Send the transaction to the network.

Example code for USDT on Node.js :
```js
const { defaultAccount } = web3.eth;
const abi = require('IERC20ABI.js')
const contractAddress = '0xdAC17F958D2ee523a2206206994597C13D831ec7';

// Initialize the contract
const Contract = new web3.eth.Contract(abi, contractAddress, { from: defaultAccount });

// Get number of decimals
const decimals = await Contract.methods.decimals().call();

// Calculate amount as int, convert to string
const tokenAmountInt = '' + amount * Math.pow(10, decimals);

// Generate abi string
const data = Contract.methods.transfer(address, tokenAmountInt).encodeABI();

// Get current gas price
const gasPrice = await web3.eth.getGasPrice();

const transaction = {
    data,
    gasPrice,
    from: defaultAccount,
    to: contractAddress,
};

// Send the transaction
const hash = await web3.eth.personal.sendTransaction(transaction, 'password123');
```

## Receive/Monitor ERC20 Token Transfers
One of the ways to monitor ERC20 token transactions is to subscribe to the contract logs.

The example below will subscribe to the transfer event logs for the USDT contract.
```js
const usdtContractAddress =  '0xdAC17F958D2ee523a2206206994597C13D831ec7';
const transferTopicHash = web3.utils.sha3('Transfer(address,address,uint256)');

web3.eth
    .subscribe('logs',
        { address: [usdtContractAddress], topics: [transferTopicHash] },
        (error) => {
            if (error) console.log('logs subscription error ', error.stack);
        }
    ).on('data',
        (log) => console.log('Received transfer log:', log)
    );
```
If you want to listen to all topics just omit the topics parameter.

In order to generate topic hash from event signature, omit everything except the event name & parameter data types. For example:  
Transfer event signature: `event Transfer(address indexed from, address indexed to, uint256 value)`  
String used for generating topic hash: `Transfer(address,address,uint256)`  

---

Further reading:
* [Contract ABI Specification](https://docs.soliditylang.org/en/v0.8.4/abi-spec.html){:target="_blank"}
* [web3.eth.subscribe("logs")](https://web3js.readthedocs.io/en/v1.3.4/web3-eth-subscribe.html#subscribe-logs){:target="_blank"}
* [web3.eth.getPastLogs](https://web3js.readthedocs.io/en/v1.3.4/web3-eth.html#eth-getpastlogs-return){:target="_blank"}


