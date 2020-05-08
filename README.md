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