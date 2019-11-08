How to do check the Keccak-256 of a string

```py
import sha3

def hash(string):
    k = sha3.keccak_256()
    k.update(string.encode())
    return k.hexdigest()

hash("balance(address):(uint64)")
```

Gives:
```
'9993021ae591e1fa9883146ba50436cf0621f8b62f21a75b9afe99f46fcc780d'
```
