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
