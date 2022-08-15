### Get Account Properties

- Following parameters required:
  - **Address** - address to get account properties from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'
public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
 
account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

with client.AccountHTTP(ENDPOINT) as http:
    properties = http.get_account_properties(account.address)
    print(properties)
```


