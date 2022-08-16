### Get AccountInfo for an account

Returns AccountInfo for an account.

- Following parameters required:
  - **Address** - address to get info from.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'
public_key = '3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2'

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

with client.AccountHTTP(ENDPOINT) as http:
    account = http.get_account_info(account.address)
    print(account)
```


### Get AccountInfo for multiple accounts

Returns AccountInfo for multiple accounts.

- Following parameters required:
  - **[]Address** - addresses to get info from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'
alice_key = '3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2'
bob_key = '990585BBB7C97BB61D90410B67552D82D30738994BA7CF2B1041D1E0A6E4169B'

alice = models.PublicAccount.create_from_public_key(alice_key, models.NetworkType.MIJIN_TEST)
bob = models.PublicAccount.create_from_public_key(bob_key, models.NetworkType.MIJIN_TEST)

with client.AccountHTTP(ENDPOINT) as http:
    accounts = http.get_accounts_info([alice, bob])
    print(accounts)
```


### Get confirmed transactions information

Gets an array of confirmed transaction for which an account is signer or recipient.
- Following parameters required:
  - **Public Account** - account to get transactions associated with

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'
public_key = '3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2'

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

with client.AccountHTTP(ENDPOINT) as http:
    txns = http.transactions(account)
    print(txns)
```
