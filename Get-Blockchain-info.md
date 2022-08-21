### Get the current height of the chain

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

with client.BlockchainHTTP(ENDPOINT) as http:
    height = http.get_blockchain_height()
    print(height)
```


### Get BlockInfo for a given block height

- Following parameters required:
  - **Height** - height of block to get info from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

height = 100

with client.BlockchainHTTP(ENDPOINT) as http:
    block = http.get_block_by_height(height)
    print(block)
```


### Get an array of BlockInfo for a given block height and limit

- Following parameters required:
  - **Height** - height of block to get info from
  - **Limit** - how many block infos to return

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

height = 100

limit = 10

with client.BlockchainHTTP(ENDPOINT) as http:
    blocks = http.get_blocks_by_height_with_limit(height, limit)
    print(blocks)
```


### Get transactions from a block

- Following parameters required:
  - **Height** - height of block to get transactions from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

height = 1

with client.BlockchainHTTP(ENDPOINT) as http:
    block = http.get_block_transactions(height)
    print(block)
```


### Get the current score of the chain

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

with client.BlockchainHTTP(ENDPOINT) as http:
    score = http.get_blockchain_score()
    print(score)
```