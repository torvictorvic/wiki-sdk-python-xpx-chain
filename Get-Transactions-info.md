### Get transaction information by transaction id or hash

- Following parameters required:
  - **Transaction ID or hash** - identifier of transaction to get info about

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

tx_hash = 'C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6'

with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transaction(tx_hash)
    print(tx_info)
```


### Get transaction informations for a given array of transaction ids or hashes

- Following parameters required:
  - **[]Transaction ID or hash** - identifiers of transaction to get info about


```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

tx_hashes = [
    'C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6',
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

ENDPOINT = '//localhost:3000'

tx_hash = 'C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6'

with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transaction_status(tx_hash)
    print(tx_info)
```

### Get transaction statuses for a given array of transaction hashes or ids

- Following parameters required:
  - **[]Transaction ID or hash** - identifier of transaction to get their statuses

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

tx_hashes = [
    'C95B61BF128BF8D7ED12B997197F2CC220BF33A19BBCF10C67B22086BED85ED6',
    '463FDEC912FC4BA3D84FEC31E8293FAE6D142FC271A71E464FDA563F056A6151'
]

with client.TransactionHTTP(ENDPOINT) as http:
    tx_info = http.get_transaction_statuses(tx_hashes)
    print(tx_info)
```