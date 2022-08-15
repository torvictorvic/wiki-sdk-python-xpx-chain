### Address Alias Transaction

Address alias transaction is used to link an address to a namespace.
To create Address Alias transaction use **NewAddressAliasTransaction()**.

- Following parameters required:
  - **Address** - The address which we want to link.
  - **NamespaceId** - The namespace id which we want to link.
  - **AliasActionType** - The type of operation `AliasLink`/`AliasUnlink`

```python
from xpxchain import models
from xpxchain import client
import os
import asyncio
 
ENDPOINT = '//localhost:3000'
NETWORK_TYPE = models.NetworkType.MIJIN_TEST
XPX = models.MosaicId(0x0dc67fbe1cad29e3) # XPX
DIVISIBILITY = 10**6
GENERATION_HASH = '7B631D803F912B00DC0CBED3014BBD17A302BA50B99D233B9C2D9533B842ABDF'
genesis_private_key = '28FCECEA252231D2C86E1BCF7DD541552BDBBEFBB09324758B3AC199B4AA7B78'
genesis = models.Account.create_from_private_key(genesis_private_key, NETWORK_TYPE)

alice = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))    
bob = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))

amount = 10000 * DIVISIBILITY

tx = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=alice.address,
    network_type=NETWORK_TYPE, 
    mosaics=[models.Mosaic(XPX, amount)]
)

signed_tx = tx.sign_with(genesis, GENERATION_HASH)

async def announce(tx):
    signer = models.PublicAccount.create_from_public_key(tx.signer, NETWORK_TYPE)
    
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        await listener.confirmed(signer.address)
        await listener.status(signer.address)
        
        async with client.AsyncTransactionHTTP(ENDPOINT, network_type=NETWORK_TYPE) as http:
            await http.announce(tx)
        
        async for m in listener:
            if ((m.channel_name == 'status') and (m.message.hash == tx.hash.upper())):
                raise errors.TransactionError(m.message)
            elif ((m.channel_name == 'confirmedAdded') and (m.message.transaction_info.hash == tx.hash.upper())):
                return m.message

tx = asyncio.run(announce(signed_tx))

import random, string
random_name = ''.join(random.choice(string.ascii_lowercase + string.digits) for _ in range(6))

tx = models.RegisterNamespaceTransaction.create_root_namespace(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    namespace_name=random_name,
    duration=60
)

signed_tx = tx.sign_with(alice, GENERATION_HASH)
tx = asyncio.run(announce(signed_tx))
namespace_id = tx.namespace_id

tx = models.AddressAliasTransaction.create(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    max_fee=1,
    action_type=models.AliasActionType.LINK,
    address=alice.address,
    namespace_id=namespace_id,
)

signed_tx = tx.sign_with(alice, GENERATION_HASH)

tx = asyncio.run(announce(signed_tx))
print(tx)
```


### Mosaic Alias Transaction

Mosaic alias transaction is used to link a mosaic to a namespace.
To create Mosaic Alias transaction use **NewMosaicAliasTransaction()**.

- Following parameters required:
  - **MosaicId** - The mosaic id which we want to link.
  - **NamespaceId** - The namespace id which we want to link.
  - **AliasActionTyp

```python
from xpxchain import models
from xpxchain import client
import os
import asyncio
 
ENDPOINT = '//localhost:3000'
NETWORK_TYPE = models.NetworkType.MIJIN_TEST
XPX = models.MosaicId(0x0dc67fbe1cad29e3) # XPX
DIVISIBILITY = 10**6
GENERATION_HASH = '7B631D803F912B00DC0CBED3014BBD17A302BA50B99D233B9C2D9533B842ABDF'
genesis_private_key = '28FCECEA252231D2C86E1BCF7DD541552BDBBEFBB09324758B3AC199B4AA7B78'
genesis = models.Account.create_from_private_key(genesis_private_key, NETWORK_TYPE)

alice = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))    
bob = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))

amount = 10000 * DIVISIBILITY

tx = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=alice.address,
    network_type=NETWORK_TYPE, 
    mosaics=[models.Mosaic(XPX, amount)]
)

signed_tx = tx.sign_with(genesis, GENERATION_HASH)

async def announce(tx):
    signer = models.PublicAccount.create_from_public_key(tx.signer, NETWORK_TYPE)
    
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        await listener.confirmed(signer.address)
        await listener.status(signer.address)
        
        async with client.AsyncTransactionHTTP(ENDPOINT, network_type=NETWORK_TYPE) as http:
            await http.announce(tx)
        
        async for m in listener:
            if ((m.channel_name == 'status') and (m.message.hash == tx.hash.upper())):
                raise errors.TransactionError(m.message)
            elif ((m.channel_name == 'confirmedAdded') and (m.message.transaction_info.hash == tx.hash.upper())):
                return m.message

tx = asyncio.run(announce(signed_tx))

nonce = models.MosaicNonce(123)
mosaic_id = models.MosaicId.create_from_nonce(nonce, alice)

tx = models.MosaicDefinitionTransaction.create(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    nonce=nonce,
    mosaic_id=mosaic_id,
    mosaic_properties=models.MosaicProperties(0x3, 3),
)

signed_tx = tx.sign_with(alice, GENERATION_HASH)
tx = asyncio.run(announce(signed_tx))

import random, string
random_name = ''.join(random.choice(string.ascii_lowercase + string.digits) for _ in range(6))

tx = models.RegisterNamespaceTransaction.create_root_namespace(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    namespace_name=random_name,
    duration=60
)

signed_tx = tx.sign_with(alice, GENERATION_HASH)
tx = asyncio.run(announce(signed_tx))
namespace_id = tx.namespace_id

tx = models.MosaicAliasTransaction.create(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    max_fee=1,
    action_type=models.AliasActionType.LINK,
    mosaic_id=mosaic_id,
    namespace_id=namespace_id,
)

signed_tx = tx.sign_with(alice, GENERATION_HASH)

tx = asyncio.run(announce(signed_tx))
print(tx)
```