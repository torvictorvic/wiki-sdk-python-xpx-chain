### Get namespace information

- Following parameters required:
  - **NamespaceID** - namespace identifier to get info about

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

namespace_name = 'foo'

namespace_id = models.NamespaceId(namespace_name)

with client.NamespaceHTTP(ENDPOINT) as http:
    namespace_info = http.get_namespace(namespace_id)
    print(namespace_info)
```


### Get namespaces an account owns

- Following parameters required:
  - **Address** - address of account to get namespaces from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

with client.NamespaceHTTP(ENDPOINT) as http:
    namespaces_info = http.get_namespaces_from_account(account.address)
    print(namespaces_info)
```


### Get readable names for a set of namespaces

- Following parameters required:
  - **[]NamespaceID** - namespace identifiers to get info about


```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

namespace_id_1 = models.NamespaceId('foo')
namespace_id_2 = models.NamespaceId('bar')

with client.NamespaceHTTP(ENDPOINT) as http:
    namespaces_name = http.get_namespaces_name([namespace_id_1, namespace_id_2])
    print(namespaces_name)
```


### Get namespace infos for a given array of addresses

- Following parameters required:
  - **[]Address** - addresses of accounts to get namespaces from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key_1 = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
public_key_2 = '73472A2E9DCEA5C2A36EB7F6A34A634010391EC89E883D67360DB16F28B9443C'

account1 = models.PublicAccount.create_from_public_key(public_key_1, models.NetworkType.MIJIN_TEST)
account2 = models.PublicAccount.create_from_public_key(public_key_2, models.NetworkType.MIJIN_TEST)

with client.NamespaceHTTP(ENDPOINT) as http:
    namespaces_info = http.get_namespaces_from_accounts([account1.address, account2.address])
    print(namespaces_info)
```