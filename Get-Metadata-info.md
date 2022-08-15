
### Get Metadata info for Address

- Following parameters required:
  - **Address** - address to get account properties from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'
public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
  
account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

with client.MetadataHTTP(ENDPOINT) as http:
    account_metadata = http.get_account_metadata(account)
    print(account_metadata)
```

### Get Metadata info for Mosaic

- Following parameters required:
  - **MosaicID** - mosaic identifier to get mosaic properties from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
nonce = 1234

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)
mosaic_id = models.MosaicId.create_from_nonce(models.MosaicNonce.create_from_int(nonce), account)

with client.MetadataHTTP(ENDPOINT) as http:
    mosaic_metadata = http.get_mosaic_metadata(mosaic_id)
    print(mosaic_metadata)
```

### Get Metadata info for Namespace

- Following parameters required:
  - **NamespaceID** - namespace identifier to get namespace properties from

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
name = 'foo'

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)
namespace = models.NamespaceName.create_from_name(name)

with client.MetadataHTTP(ENDPOINT) as http:
    namespace_metadata = http.get_namespace_metadata(namespace.namespace_id)
    print(namespace_metadata)
```