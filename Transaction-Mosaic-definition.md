### Mosaic definition transaction

Before a mosaic can be created or transferred, a corresponding
definition of the mosaic has to be created and published to the network.
This is done via a **NewMosaicDefinitionTransaction()**.

- Following parameters are required:
  - **Nonce** - TODO
  - **Private Key** - Private key of the mosaic owner. Mosaic rental fees will be paid from this account.
  - **Mosaic properties**:
      - supply mutable - The creator can choose between a definition
      that allows a mosaic supply change at a later point or a
      immutable supply. In the first case the creator is only allowed
      to decrease the supply within the limits of mosaics owned.
      - transferability - The creator can choose if the mosaic can be
      transferred to and from arbitrary accounts, or only allowing itself
      to be the recipient once transferred for the first time.
      - divisibility - Determines up to what decimal place the mosaic can
      be divided. Divisibility of 3 means that a mosaic can be divided
      into smallest parts of 0.001 mosaics. The divisibility must be in
      the range of 0 and 6.
      - duration - The number of confirmed blocks we would like to rent
      our namespace for. Should be inferior or equal to namespace duration.

```python
from xpxchain import models
from xpxchain import client
from xpxchain import errors
import os
import asyncio

ENDPOINT = "//bctestnet1.brimstone.xpxsirius.io:3000"
NETWORK_TYPE = models.NetworkType.TEST_NET
GENERATION_HASH = "56D112C98F7A7E34D1AEDC4BD01BC06CA2276DD546A93E36690B785E82439CA9"

ACCOUNT_PRIVATE_KEY = "FILL YOUR PRIVATE KEY HERE"
ACCOUNT = models.Account.create_from_private_key(ACCOUNT_PRIVATE_KEY, NETWORK_TYPE)

NONCE = models.MosaicNonce.create_random()
MOSAIC_ID = models.MosaicId.create_from_nonce(NONCE, ACCOUNT)
DIVISIBILITY = 0
DURATION = 10000
MOSAIC_PROPERTIES = models.MosaicProperties.create(
  supply_mutable=True,
  transferable=True,
  divisibility=DIVISIBILITY,
  duration=DURATION
)

tx = models.MosaicDefinitionTransaction.create(
  deadline=models.Deadline.create(),
  nonce=NONCE,
  mosaic_id=MOSAIC_ID,
  mosaic_properties=MOSAIC_PROPERTIES,
  network_type=NETWORK_TYPE
)

signed_tx = tx.sign_with(ACCOUNT, GENERATION_HASH)

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

print(tx)
```