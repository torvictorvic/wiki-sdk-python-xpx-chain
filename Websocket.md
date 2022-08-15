**WebSockets make possible receiving notifications when a transaction or event occurs in the blockchain.
The notification is received in real time without having to poll the API waiting for a reply.**


### Subscribe block

The block channel notifies for every new block. The message contains the block information.

```python
from xpxchain import models
from xpxchain import client
import asyncio
 
ENDPOINT = '//localhost:3000'
NETWORK_TYPE = models.NetworkType.MIJIN_TEST

async def listen():
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        await listener.new_block()
        
        async for m in listener:
            print(m.message.height)

asyncio.run(listen())
```


### Subscribe confirmed added

The confirmed added channel notifies when a transaction related to an address is included in a block. The message contains the transaction.

```python
from xpxchain import models
from xpxchain import client
import asyncio
 
ENDPOINT = '//localhost:3000'
NETWORK_TYPE = models.NetworkType.MIJIN_TEST

alice = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))    

async def listen(account):
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        await listener.confirmed(account.address)
        
        async for m in listener:
            print(m.message)

asyncio.run(listen(alice))
```


### Subscribe partial added

The partial added channel notifies when an aggregate bonded transaction related to an address is in partial state and waiting to have all required cosigners. The message contains a transaction.

```python
from xpxchain import models
from xpxchain import client
import asyncio
 
ENDPOINT = '//localhost:3000'
NETWORK_TYPE = models.NetworkType.MIJIN_TEST

alice = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))    

async def listen(account):
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        await listener.aggregate_bonded_added(account.address)
        
        async for m in listener:
            print(m.message)

asyncio.run(listen(alice))
```


### Subscribe status

The status channel notifies when a transaction related to an address rises an error. The message contains the error message and the transaction hash.

```python
from xpxchain import models
from xpxchain import client
import asyncio
 
ENDPOINT = '//localhost:3000'
NETWORK_TYPE = models.NetworkType.MIJIN_TEST

alice = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))    

async def listen(account):
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        await listener.status(account.address)
        
        async for m in listener:
            print(m.message)

asyncio.run(listen(alice))
```