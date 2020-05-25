Use namedtuples
```python
from collections import namedtuple
Appliance = namedtuple('Appliance', ['designation','voltage', 'weight', 'domain'])
bld = Appliance('blender', 220, 3, 'the kitchen')
print(bld)
print(bld[1])
"""
Appliance(designation='blender', voltage=220, weight=3, domain='the kitchen')
220
"""
```

Format an integer to hexadecimal representation
```python
"0x{:02X}".format(254)

"""
'0xFE'
"""
```
Build a dict using comprehensions
```python
normal_size = ['m','l','s']
bigger = {a : 'x' + a for a in normal_size}
print(bigger)
"""
{'m': 'xm', 'l': 'xl', 's': 'xs'}
"""
```

Use setter and getter decorators
```python
class Animal(object):
    def __init__(self, weight):
        self._weight = weight
        print("that raccoon weights",weight,"kgs")

    @property
    def weight(self):
        return self._weight

    @weight.setter
    def weight(self, value):
        diff = value - self._weight
        if diff > 0:
            print("that racoon got", diff,"kgs fatter")
        if diff < 0:
            print("that racoon lost", abs(diff),"kgs")
        print("now it's",value,"kgs")
        self._weight = value

racoon = Animal(10)
racoon.weight = 8

"""
that raccoon weights 10 kgs
that racoon lost 2 kgs
now it's 8 kgs
"""
```
Consume an Iterable in chunks of fixed size
```python
from itertools import chain, islice
def chunks(iterable, size=3) -> Iterable[Iterable]:
  """
  Slices an iterable into an iterable of iterables of fixed size
  """
    iterator = iter(iterable)
    for first in iterator:
        yield chain([first], islice(iterator, size - 1))

bread = ["1","2","3","4","5","6","7"]
for i, plate in enumerate(chunks(bread, 3), 1):
  print(list(plate))
"""
['1', '2', '3']
['4', '5', '6']
['7']
"""
```

Patch requests.get to test network methods
```python
from unittest.mock import Mock, create_autospec, patch
import pytest
import requests
import json
from typing import List, Dict, Tuple
import attr

class PlayMock(object):

    def get_api(self, url:str="https://blank.org", data:Dict) -> Dict:
        """
        call an url with the given data
        @param url: some url
        @return : content
        """
        print("opening %s" % url)
        stk = requests.get(url, data=data)
        content = stk.text
        print("content was %s" % content)
        return stk.json()

    @patch("requests.get")
    def test_get_api(self, mock_req_get):
        obj = {"olives": "several", "skying": ["fast", "not fast"]}
        mock_req_get.return_value = Mock(ok=True, text = json.dumps(obj))
        mock_req_get.return_value.json.return_value = obj
        scam = self.get_api("http://friendoptions.net", data={"best_friend": "dog"})
        assert "olives" in scam.keys()


PlayMock().test_get_api()
```
Build an url
```python
from urllib.parse import urlunparse
scheme = "http"
domain = "www.barney.ru"
port = 80
ext = "/auth/users/login"
r = urlunparse((scheme, '%s:%d' % (domain, port), ext, 'user', 'admin', 'anchor3'))
print(r)

"""
http://www.barney.ru:80/auth/users/login;user?admin#anchor3
"""

```

## Cryptography ##
Encrypt/decrypt a message
```python
from Crypto.Cipher import PKCS1_OAEP
from Crypto.PublicKey import RSA
"""
generate a private key:
openssl genrsa -out private.pem 1024
derive the public key
openssl rsa -in private.pem -outform PEM -pubout -out public.pem
"""

# message  = b"e24652949e9d317e49da842b6b651cca098be3a6819e45ddcb518873f890f5a9"
message  = "Du café pour la maternité".encode('utf-8')

with open("private.pem","rb") as kf:
    pkeys = kf.read()
    pvkey = RSA.importKey(pkeys)

with open("message.enc","wb") as wr:
    key = RSA.importKey(open('public.pem').read())
    cipher = PKCS1_OAEP.new(key)
    ciphertext = cipher.encrypt(message)
    wr.write(ciphertext)

with open("message.enc","rb") as wo:
    ciphertext = wo.read()
    decipher = PKCS1_OAEP.new(pvkey)
    msg = decipher.decrypt(ciphertext).decode('utf-8')
    print(msg)

```
Stream through a huge file to compute its checksum
```python
import hashlib
def get_fingerprint(file) -> str:
    h256 = hashlib.sha256()
    BUF_SIZE = 65536
    with open(file, "rb") as f:
        while True:
            data = f.read(BUF_SIZE)
            if not data:
                break
            h256.update(data)

    return h256.hexdigest()

r = get_fingerprint("one_big_file.exe")
print(r)
```

## Pandas ##
Apply a method to each element in a DataFrame to create a new column
```python
import pandas as pd

def check_price(oyster_type):
    if oyster_type == "expensive":
        return "45€"
    elif oyster_type == "free":
        return 0
    else:
        return "unknown"

menu = pd.DataFrame({"oyster":["expensive","free","reasonable"]})
menu["price"] = menu['oyster'].apply(check_price)
print(menu)
"""
oyster    price
0   expensive      45€
1        free        0
2  reasonable  unknown
"""
```
