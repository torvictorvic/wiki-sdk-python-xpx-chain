
### Aggregate complete transaction

Aggregate Complete transaction is a method to execute multiple
transactions at once. To be valid, all transactions included in
aggregate complete should be signed. If valid, it will be
included in a block.

- Following parameters required:
  - **Inner transactions** - array of transactions signed by owners.
  Other aggregate transactions are not allowed as inner transactions.

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

tx = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=bob.address,
    network_type=NETWORK_TYPE, 
    mosaics=[models.Mosaic(XPX, amount)]
)

signed_tx = tx.sign_with(genesis, GENERATION_HASH)
tx = asyncio.run(announce(signed_tx))


alice_to_bob = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=bob.address,
    mosaics=[models.Mosaic(XPX, 1*DIVISIBILITY)],
    network_type=NETWORK_TYPE,
)

bob_to_alice = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=alice.address,
    mosaics=[models.Mosaic(XPX, 1*DIVISIBILITY)],
    network_type=NETWORK_TYPE,
)

tx = models.AggregateTransaction.create_complete(
    deadline=models.Deadline.create(),
    inner_transactions=[bob_to_alice.to_aggregate(bob), alice_to_bob.to_aggregate(alice)],
    network_type=NETWORK_TYPE,
)

signed_tx = tx.sign_transaction_with_cosignatories(alice, GENERATION_HASH, [bob])
tx = asyncio.run(announce(signed_tx))
print(tx)
```