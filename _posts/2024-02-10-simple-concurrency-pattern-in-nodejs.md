---
layout: post
categories: js
---

```js
async function getData() {
    // simulate long running op
    await new Promise(r => setTimeout(r, 2000));
    return true;
}

async function process() {
    const batch = [];
    const batchSize = 50;
    for (let i = 0; i < batchSize; i++) {
        batch.push(getData());
    }
    await Promise.all(batch);
}
```