### Get transaction information by transaction id or hash

- Following parameters required:
  - **Transaction ID or hash** - identifier of transaction to get info about

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

TX_HASH = '47639853CC4062A0585EF386E4EF96F9776DE33562F0DEC352E86D50D9B835A0'

with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transaction(TX_HASH)
    print(tx_info)
```


### Get transaction informations for a given array of transaction ids or hashes

- Following parameters required:
  - **[]Transaction ID or hash** - identifiers of transaction to get info about


```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

tx_hashes = [
    '47639853CC4062A0585EF386E4EF96F9776DE33562F0DEC352E86D50D9B835A0',
    '463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151'
]

with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transactions(tx_hashes)
    print(tx_info)
```


### Get transaction status for transaction hash or id

- Following parameters required:
  - **Transaction ID or hash** - identifier of transaction to get its status

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

TX_HASH = '117A0DF115F179A2D99BA9AF288B40CCB22F8BD338B5891ECC578995692B479F'

print (" get_transaction_status \n ")
with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transaction_status(TX_HASH)
    print(tx_info)
```


### Get transaction statuses for a given array of transaction hashes or ids

- Following parameters required:
  - **[]Transaction ID or hash** - identifier of transaction to get their statuses

```python
from xpxchain import models
from xpxchain import client


ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

TX_HASHS = ["117A0DF115F179A2D99BA9AF288B40CCB22F8BD338B5891ECC578995692B479F", "4DC2D37600EA2401D732275713087E6916FD3B48D1E7B1F18A0F997CC404A461"]
print (" get_transaction_statuses [POST] \n ")

with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transaction_statuses(TX_HASHS)
    print(tx_info)
```



### Get incoming transactions info

- Following parameters required:
  - **plain_address** - plain address of the public key of the account.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

PLAIN_ADDRESS = "VDBV54TD7W2B7ZUDWVRYFV2CG7KVCYRXUIPK53KU"

with client.AccountHTTP(ENDPOINT) as http:
    tx_info = http.incoming_transactions(PLAIN_ADDRESS)
    print(tx_info)
```


### Get outgoing transactions info

- Following parameters required:
  - **public_key** - public key of account.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

P_KEY = "3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2"

with client.AccountHTTP(ENDPOINT) as http:
    tx_info = http.outgoing_transactions(P_KEY)
    print(tx_info)

```



### Get outgoing transactions info

- Following parameters required:
  - **public_key** - public key of account.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

P_KEY = "3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2"

with client.AccountHTTP(ENDPOINT) as http:
    tx_info = http.outgoing_transactions(P_KEY)
    print(tx_info)

```


### Get unconfirmed transactions info

- Following parameters required:
  - **public_key** - public key of account.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

P_KEY = "990585BBB7C97BB61D90410B67552D82D30738994BA7CF2B1041D1E0A6E4169B"

with client.AccountHTTP(ENDPOINT) as http:
    tx_info = http.unconfirmed_transactions(account.address.address)
    print(tx_info)
```


### Get unconfirmed transactions info

- Following parameters required:
  - **public_key** - public key of account.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

P_KEY = "990585BBB7C97BB61D90410B67552D82D30738994BA7CF2B1041D1E0A6E4169B"

with client.AccountHTTP(ENDPOINT) as http:
    tx_info = http.unconfirmed_transactions(account.address.address)
    print(tx_info)
```


### Aggregate bonded transactions

- Following parameters required:
  - **public_key** - public key of account.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

P_KEY = "990585BBB7C97BB61D90410B67552D82D30738994BA7CF2B1041D1E0A6E4169B"

with client.AccountHTTP(ENDPOINT) as http:
    tx_info = http.aggregate_bonded_transactions(account.address.address)
    print(tx_info)
```
