### Get AccountInfo for an account

Returns AccountInfo for an account.

- Following parameters required:
  - **Address** - address to get info from.

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'
public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'

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

ENDPOINT = '//localhost:3000'
alice_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
bob_key = '73472A2E9DCEA5C2A36EB7F6A34A634010391EC89E883D67360DB16F28B9443C'

alice = models.PublicAccount.create_from_public_key(alice_key, models.NetworkType.MIJIN_TEST)
bob = models.PublicAccount.create_from_public_key(bob_key, models.NetworkType.MIJIN_TEST)

with client.AccountHTTP(ENDPOINT) as http:
    accounts = http.get_accounts_info([alice.address, bob.address])
    print(accounts)
```


### Get confirmed transactions information

Gets an array of confirmed transaction for which an account is signer or recipient.
- Following parameters required:
  - **Public Account** - account to get transactions associated with

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'
public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

with client.AccountHTTP(ENDPOINT) as http:
    txns = http.transactions(account)
    print(txns)
```
