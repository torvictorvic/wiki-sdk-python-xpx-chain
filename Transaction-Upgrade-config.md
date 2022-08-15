### Network Config Transaction

Network config transaction is used to update pre-defined Siriuc-Chain network in runtime.
To create update network config transaction use **NewNetworkConfigTransaction()**.

- Following parameters required:
  - **Duration** - Blocks count, after which updated config will be applied.
  - **BlockChainConfig**
  - **SupportedEntityVersions**

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

with client.ConfigHTTP(f'{ENDPOINT}',  network_type=NETWORK_TYPE) as http:
    config = http.get_config(100)

del config.network_config.sections['plugin:catapult.plugins.transfer']

tx = models.NetworkConfigTransaction.create(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    apply_height_delta=500,
    network_config=config.network_config.to_string(),
    supported_entity_versions=config.supported_entity_versions,
)

signed_tx = tx.sign_with(genesis, GENERATION_HASH)
tx = asyncio.run(announce(signed_tx))
print(tx)
```

### Blockchain Upgrade Transaction

Blockchain upgrade transaction is used to update version of Sirius-Chain.
To create update network config transaction use **NewBlockchainUpgradeTransaction()**.

- Following parameters required:
  - **Duration** - Blocks count, after which updated config will be applied.
  - **Version** - Version of Sirius-Chain to be set.

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

tx = models.BlockchainUpgradeTransaction.create(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    new_blockchain_version=2,
    upgrade_period=500,
)

signed_tx = tx.sign_with(genesis, GENERATION_HASH)
tx = asyncio.run(announce(signed_tx))
print(tx)
```