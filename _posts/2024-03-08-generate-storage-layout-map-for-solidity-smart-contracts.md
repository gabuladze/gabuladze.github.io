---
layout: post
categories: solidity
---

1. Create the input json file for solc. let's call it `in.json`.
```json
{
  "language": "Solidity",
  "sources": {
    "Contract.sol": {
      "urls": [
        "/path/to/contract.sol"
      ]
    }
  },
  "settings": {
    "outputSelection": {
      "*": {
        "*": [
          "storageLayout"
        ]
      }
    }
  }
}
```

2. Generate the layout map:
```
solc --allow-paths . --standard-json < ./in.json > out.json
```