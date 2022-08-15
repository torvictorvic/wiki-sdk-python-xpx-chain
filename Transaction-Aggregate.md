### Aggregate bonded transactions

An aggregate transaction is considered to be bonded if requires signatures from other participants.

- When sending an aggregate bonded transaction, an account must first
announce and get confirmed a Lock Funds Transaction for this aggregate
with at least 10 XPX. This mechanism is required to prevent network spamming.
- Once the related aggregate bonded transaction is confirmed, locked
funds become available again in the account that signed the initial lock funds transaction.

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

async def announce_partial(tx):
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        address = models.PublicAccount.create_from_public_key(tx.signer, NETWORK_TYPE).address
        
        await listener.aggregate_bonded_added(address)
        await listener.status(address)
        
        async with client.AsyncTransactionHTTP(ENDPOINT, network_type=NETWORK_TYPE) as http:
            await http.announce_partial(tx)
        
        async for m in listener:
            if ((m.channel_name == 'status') and (m.message.hash == tx.hash.upper())):
                raise errors.TransactionError(m.message)
            elif ((m.channel_name == 'partialAdded') and (m.message.transaction_info.hash == tx.hash.upper())):
                return m.message

async def announce_cosignature(tx):
    async with client.Listener(f'{ENDPOINT}/ws', network_type=NETWORK_TYPE) as listener:
        address = models.PublicAccount.create_from_public_key(tx.signer, NETWORK_TYPE).address
        
        await listener.confirmed(address)
        await listener.status(address) 
        
        async with client.AsyncTransactionHTTP(ENDPOINT, network_type=NETWORK_TYPE) as http:
            await http.announce_cosignature(tx)
        
        async for m in listener:
            if ((m.channel_name == 'status') and (m.message.hash == tx.hash.upper())):
                raise errors.TransactionError(m.message)
            elif ((m.channel_name == 'confirmedAdded') and (m.message.transaction_info.hash == tx.parent_hash.upper())):
                return m.message

amount = 10000 * DIVISIBILITY

tx = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=alice.address,
    network_type=NETWORK_TYPE, 
    mosaics=[models.Mosaic(XPX, amount)]
)

signed_tx = tx.sign_with(genesis, GENERATION_HASH)
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

bonded = models.AggregateTransaction.create_bonded(
    deadline=models.Deadline.create(),
    inner_transactions=[bob_to_alice.to_aggregate(bob), alice_to_bob.to_aggregate(alice)],
    network_type=NETWORK_TYPE,
)

signed_bonded = bonded.sign_transaction_with_cosignatories(alice, GENERATION_HASH)

lock = models.LockFundsTransaction.create(
    deadline=models.Deadline.create(),
    network_type=NETWORK_TYPE,
    mosaic=models.Mosaic(XPX, 10*DIVISIBILITY),
    duration=60,
    signed_transaction=signed_bonded,
)

signed_lock = lock.sign_with(alice, GENERATION_HASH)

tx = asyncio.run(announce(signed_lock))
tx = asyncio.run(announce_partial(signed_bonded))

with client.AccountHTTP(ENDPOINT, network_type=NETWORK_TYPE) as http:
    reply = http.aggregate_bonded_transactions(bob)
    signed_cosig = models.CosignatureTransaction.create(reply[0]).sign_with(bob)

tx = asyncio.run(announce_cosignature(signed_cosig))
print(tx)
```