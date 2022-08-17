
### Get mosaic information

- Following parameters required:
  - **MosaicID** - mosaic identifier to get info about

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

PUBLIC_KEY = '3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2'
account   = models.PublicAccount.create_from_public_key( PUBLIC_KEY, models.NetworkType.TEST_NET)

with client.AccountHTTP(ENDPOINT) as http:
    accounts = http.get_accounts_info([account])
    with client.MosaicHTTP(ENDPOINT) as httpm:
        mosaic_info = httpm.get_mosaic( format(accounts[0].mosaics[0].id.id, "x") )
        print(mosaic_info)

```


### Get information for array of mosaics

- Following parameters required:
  - **[]MosaicID** - mosaic identifiers to get info about

```python
from xpxchain import models
from xpxchain import client


PUBLIC_KEY_1 = '3DB3DC590716ABFF18CA078D4843D4DDFD41934F6D327C17DBD12946C1172CF2'
PUBLIC_KEY_2 = '990585BBB7C97BB61D90410B67552D82D30738994BA7CF2B1041D1E0A6E4169B'

account_1 = models.PublicAccount.create_from_public_key( PUBLIC_KEY_1, models.NetworkType.TEST_NET)
account_2 = models.PublicAccount.create_from_public_key( PUBLIC_KEY_2, models.NetworkType.TEST_NET)

with client.AccountHTTP(ENDPOINT) as http:
    accounts = http.get_accounts_info([account_1, account_2])    

    with client.MosaicHTTP(ENDPOINT) as httpm:
        mosaics_info = httpm.get_mosaics([ format(accounts[0].mosaics[0].id.id, "x") , format(accounts[1].mosaics[0].id.id, "x") ])
        print( mosaics_info )

```


### Get information for mosaic linked to namespace

- Following parameters required:
  - **NamespaceId** - namespace id which mosaic linked to

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = 'https://bctestnet3.brimstone.xpxsirius.io'

public_key = '0EB448D07C7CCB312989AC27AA052738FF589E2F83973F909B506B450DC5C4E2'
namespace_name = 'foo'

namespace_id = models.NamespaceId(namespace_name)

with client.NamespaceHTTP(ENDPOINT) as http:
    linked_mosaics = http.get_linked_mosaic_id(namespace_id)
    print(linked_mosaics)
```