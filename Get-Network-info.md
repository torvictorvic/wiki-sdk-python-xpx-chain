### Get the current network type of the chain

```python
from xpxchain import models
from xpxchain import client

ENDPOINT = '//localhost:3000'

with client.NetworkHTTP(ENDPOINT) as http:
    network_type = http.get_network_type()
    print(network_type)
```