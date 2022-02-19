# Decoupled JSON views
This isn't a project structure in itself, but rather a pattern that can be used in any other project structure. Python dictionaries can
generally be serialized to JSON, and web frameworks (such as Flask) allow for `dict` objects to be treated as JSON responses. This is
one way of strongly typing these responses.

## Project structure
```
...
|-views
| |-__init__.py
| |-view.py
| |-someview.py
| \-...
\-...
```

### Views
`view.py` exports the following class:

```py
from abc import ABC

class View(ABC):
    def to_dict(self):
        return dict(self.__dict__)
```

Note that the additional `dict()` call is necessary in order to ensure that the dictionary is hashable (required for JSON serialization). This will
fail in JSON serialization if any of the class members aren't serializable, but extending this method to handle each type appropriately will be left
as an exercise to the reader (it's not particularly difficult).

Any additional views (`someview.py` etc.) would be of the form:
```py
class SomeView(View):
    def __init__(self, some_field: int):
        self.some_field = some_field
```

### Principles
Don't give views much (if any) logic beyond what is necessary to serialize them correctly. Also, avoid referencing web framework types in views - the
point of them is to stay as decoupled from the web framework as possible.

### Usage
Views can then be used like regular classes. Be sure to call the `to_dict()` method when returning one as a `dict`.

```py
@app.route('/')
def index():
    return SomeView(0).to_dict()
```

## Benefits
- JSON responses can be strongly-typed.
- Views aren't required to have any logic, unlike Flask's `MethodView` etc.
- Views are decoupled (by default) from any framework-specific concepts, so they usually won't need to change if you switch frameworks.

## Drawbacks
- The term "view" can be confused with Flask concepts with the same name.
