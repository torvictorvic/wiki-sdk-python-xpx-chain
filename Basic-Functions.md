### Generates new Keypair

```python
from xpxchain import models

account = models.Account.generate_new_account(models.NetworkType.MIJIN_TEST)

print(account.public_key)
print(account.private_key)
```

### Create an Address from a given public key

* first param - A public key in hex.
* second param - A NetworkType:
  * MainNet = Main net network.
  * TestNet = Test net network.
  * Mijin = Mijin net network.
  * MijinTest= Mijin test net network.
* Return - An Address struct
* The Address structure describes an public Address, NetworkType.

```python
from xpxchain import models

public_key = '04dd376196603c44a19fd500492e5de12de9ed353de070a788cb21f210645613'

account = models.PublicAccount.create_from_public_key(public_key, models.NetworkType.MIJIN_TEST)

print(account.address.address)
print(account.address.network_type)
```

### Create an Account from a given private key

* first param - A private key in hex.
* second param - A NetworkType:
  * MainNet = Main net network.
  * TestNet = Test net network.
  * Mijin = Mijin net network.
  * MijinTest= Mijin test net network.
* Return - A Account struct
* The Account structure describes an account private key, public key and address.


```python
from xpxchain import models

private_key = '04dd376196603c44a19fd500492e5de12de9ed353de070a788cb21f210645613'

account = models.Account.create_from_private_key(private_key, models.NetworkType.MIJIN_TEST)

print(account.address.address)
print(account.public_key)
print(account.private_key)
```

