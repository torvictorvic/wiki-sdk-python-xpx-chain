
### Get mosaic information

- Following parameters required:
  - **MosaicID** - mosaic identifier to get info about

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
nonce = 1234

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)
mosaic_id = models.MosaicId.create_from_nonce(models.MosaicNonce.create_from_int(nonce), account)

with client.MosaicHTTP(ENDPOINT) as http:
    mosaic_info = http.get_mosaic(mosaic_id)
    print(mosaic_info)
```


### Get information for array of mosaics

- Following parameters required:
  - **[]MosaicID** - mosaic identifiers to get info about

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
nonce1 = 1234
nonce2 = 12345

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)
mosaic1_id = models.MosaicId.create_from_nonce(models.MosaicNonce.create_from_int(nonce), account)
mosaic2_id = models.MosaicId.create_from_nonce(models.MosaicNonce.create_from_int(nonce), account)

with client.MosaicHTTP(ENDPOINT) as http:
    mosaics_info = http.get_mosaics([mosaic1_id, mosaic2_id])
    print(mosaics_info)
```


### Get information for mosaic linked to namespace

- Following parameters required:
  - **NamespaceId** - namespace id which mosaic linked to

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
namespace_name = 'foo'

namespace_id = models.NamespaceId(namespace_name)

with client.NamespaceHTTP(ENDPOINT) as http:
    linked_mosaics = http.get_linked_mosaic_id(namespace_id)
    print(linked_mosaics)
```