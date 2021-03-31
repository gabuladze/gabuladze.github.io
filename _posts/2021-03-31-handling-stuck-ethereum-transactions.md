---
layout: post
---
_The code snippets in the post use web3.js, decimal.js and assume that you are connected to a geth node._


| ![Ethereum transactions stuck on pending](/assets/blog/posts/2021-03-31-handling-stuck-ethereum-transactions/stuck-txs.png) |
|:--:| 
| *Stuck Transactions on Eth* |

If you have seen and experienced something similar to the screenshot, then you know what I'm talking about. :)

## Why are they stuck?
* Because the gas price fluctuates and the gas price specified in your transaction is lower than the accepted average at the time. 
* Gas price is ok, but there is a pending transaction with lower nonce on the account.

### What is gas & gas price?
The _gas value_ of a transaction tells miners how much computational resources will be needed to mine the transaction.  
The _gas price_ tells miners how much Ether you're willing to pay for the unit of work (gas).  
Therefore, this is how the transaction fees are calculated in a nutshell: `gasNeeded * gasPrice` .

## The solutions
### Try to calculate optimal gas prices to avoid transactions getting stuck
This is the first step in fixing the problem for future transactions. For this purpose you could use:
* [Etherscan's gastracker API](https://etherscan.io/apis#gastracker)
* Record gas prices from last N blocks, calculate the average and add a small cushion.
* Or If you don't care too much about the fee amount and just want to transfer a quickly as possible, just multiply the gasPrice received from your ethereum client by some number.

**Note: The methods below do not guarantee that the last submitted transaction will b processed. It just increases the probability! Therefore, for reliable transaction handling, every transaction has to be tracked!**

### Replace pending transaction with same transaction but with higher gas price
As you might have seen Ethereum transactions that belong to single account execute in strict order. This order is controlled by the `nonce` value of transaction.   
In order to increase the probability of a transaction getting confirmed you can:
* Fetch the transaction data with 
```
const transaction = await web3.eth.getTransaction(transactionHash);
```
* Send the transaction again, but this time increase the `gasPrice` by 10%: 
```
const hash = await web3.eth.personal.sendTransaction({
    from: transaction.from,
    to: transaction.to,
    // Use decimal to avoid any precision errors. Gas price is specified in wei, so it must be an integer
    gasPrice: Decimal.mul(transaction.gasPrice, 1.1).round().toNumber(), 
    gas: transaction.gas,
    value: transaction.value,
    data: transaction.input
}, 'MyPassword!'));
```
  
FYI, Some refer to this as a 'speedup' transaction.  

### What if I don't want to send it anymore?
Similarly you can:
* Fetch the transaction data with 
```
const transaction = await web3.eth.getTransaction(transactionHash);
```
* Send the transaction again, but this time set **value to 0** and increase the `gasPrice` by 10%: 
```
const hash = await web3.eth.personal.sendTransaction({
    from: transaction.from,
    to: transaction.to,
    // Use decimal to avoid any precision errors. Gas price is specified in wei, so it must be an integer
    gasPrice: Decimal.mul(transaction.gasPrice, 1.1).round().toNumber(), 
    gas: transaction.gas,
    value: '0',
    data: transaction.input
}, 'MyPassword!'));
```
  
FYI, Some refer to this as a 'cancel' transaction.  