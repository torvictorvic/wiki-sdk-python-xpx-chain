
### Modify multisig account transaction

Modify multisig account transaction is used to create multisig
account or edit it's properties. This is done via a
**NewModifyMultisigAccountTransaction()**.

- Following parameters are required:
  - **Minimum Approval Delta** - The number of signatures needed to approve a transaction.
  - **Minimum Removal Delta** - The number of signatures needed to remove a cosignatory.
  - **Modifications** - Array of cosigner accounts added or removed from the
    multisignature account. Multsig cosignatory modification type. Supported types are:
      - `sdk.Add`: Add cosignatory.
      - `sdk.Remove`: Remove cosignatory.

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
charlie = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))
multisig = models.Account.generate_new_account(NETWORK_TYPE, entropy=lambda x: os.urandom(32))

amount = 10000 * DIVISIBILITY

tx = models.TransferTransaction.create(
    deadline=models.Deadline.create(),
    recipient=multisig.address,
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

change_to_multisig = models.ModifyMultisigAccountTransaction.create(
    deadline=models.Deadline.create(),
    min_approval_delta=2,
    min_removal_delta=1,
    modifications=[
        models.MultisigCosignatoryModification.create(alice, models.MultisigCosignatoryModificationType.ADD),
        models.MultisigCosignatoryModification.create(bob, models.MultisigCosignatoryModificationType.ADD),
    ],
    network_type=NETWORK_TYPE,
)

tx = models.AggregateTransaction.create_complete(
    deadline=models.Deadline.create(),
    inner_transactions=[change_to_multisig.to_aggregate(multisig)],
    network_type=NETWORK_TYPE,
)

signed_tx = tx.sign_transaction_with_cosignatories(multisig, GENERATION_HASH, [alice, bob])
tx = asyncio.run(announce(signed_tx))
print(tx)
```