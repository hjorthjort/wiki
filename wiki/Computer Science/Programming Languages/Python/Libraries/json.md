Translate between Python list-and-record-structures, and JSON strings.
The thing you want to do is probably the following:

```python
import json
json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
```

Output:
```python
[u'foo', {u'bar': [u'baz', None, 1.0, 2]}]
```

Or this:


```python
import json
json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
```

Output:
```python
'["foo", {"bar": ["baz", null, 1.0, 2]}]'
```

There is more fancy formatting stuff, see here: <https://docs.python.org/2/library/json.html>
