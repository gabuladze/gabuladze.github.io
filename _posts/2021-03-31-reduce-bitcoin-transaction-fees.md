---
layout: post
tags: crypto btc
---

As you might know Bitcoin uses the UTXO model, which means that the inputs for each transaction are the unspent outputs of previous transactions.

As for the transaction fees, they depend on transaction size in bytes.

### How is transaction byte size calculated?
I won't go into much detail, but the important part is this:
```
i*180 + o*34
```
* i = Number of inputs
* o = Number of outputs

_Note: That is not the full formula of-course, but the rest is irrelevant at the moment._

So, if we tweak the number of inputs & outputs, we can save on transaction fees.

### Ok, but how much can I save?
Let's say you want to send BTC to 2 different addresses from your wallet and currently have 1 unspent output.  
If you were to send it as two transactions, the total size would be around: 
```
(180 + 34) * 2 = 428 bytes
```

But, if you create 1 transaction with 2 outputs, the total size would be around 
```
180 + 34 * 2 = 248 bytes
```
At the time of writing this post the fee is around *80 satoshis per byte*, and 1 BTC costs about $58000.  
So the final savings would be:
```
(428 - 248) * 80 = 14400 satoshis = 0.000144 BTC = 8.35 USD
```

### Can I decrease the number of inputs?
Let's consider [this transaction](https://blockchair.com/bitcoin/transaction/5519fee8179e790d04ab17bb1de7cad766cdf32eba170bfe8e7c18fcf34d9367){:target="_blank"}  
This happens when your wallet doesn't have an unspent output large enough to fully cover the amount you want to send. In such cases your wallet software will try to use the inputs it has to fulfill your request.

#### Possible solution
The simplest way to avoid this is to determine the average withdrawal amount from the wallet and combine your unspent outputs into 1 or several larger outputs.
This can be achieved by sending a transaction from your wallet again to your wallet address. **Beware**: The funds that you will send in this transaction will be unavailable for spending until the transaction is confirmed. Keep this in mind especially if you've specified a very low network fee, as in this case it might take hours maybe even days for the transaction to get mined.

---
##### Further reading:
* [Techniques to reduce transaction fees](https://en.bitcoin.it/wiki/Techniques_to_reduce_transaction_fees){:target="_blank"}
