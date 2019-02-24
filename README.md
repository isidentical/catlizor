# Catlizor
## Installation
You can install catlizor from PyPI.
```
$ pip(3) install catlizor
```

## Examples
```py
from catlizor import catlizor
from functools import partial

def fancy_print(st, name, *args, **kwargs):
    print(f"{st:8} {name} call with {args} and {kwargs}")
    
options = {
    "watch": [
        {
            "attribs": ["name"],
            "hooks": {
                "pre": [partial(fancy_print, 'pre')],
                "post": [partial(fancy_print, 'post')],
                "value": [partial(fancy_print, 'value')]
            },
            "exclude": {
                "pre": ["set", "del"],
                "post": ["get"],
                "value": ["set", "del"]
            }
        },
        {
            "attribs": ["age"],
            "hooks": {
                "value": [lambda name, *a, **k: print(f"Got value of {name}({', '.join(a[1:])}), {k['value']}")]
            },
            "exclude": {
                "value": ["set", "del"]
            }
        }
    ]
}

@catlizor(**options)
class MyClass:
    def __init__(self, name, age=15):
        self.name = "batuhan"
        self.age = age
    def __repr__(self):
        return "MyClass instance"
    
mc = MyClass("batuhan")
mc.__dict__["birth"] = 2019 - mc.age
info = f"{mc.name} {mc.age} years old."
del mc.name
```

and output is

```
post     __setattr__ call with (MyClass instance, 'name', 'batuhan') and {}
Got value of __getattribute__(age), 15
pre      __getattribute__ call with (MyClass instance, 'name') and {}
value    __getattribute__ call with (MyClass instance, 'name') and {'value': 'batuhan'}
Got value of __getattribute__(age), 15
post     __delattr__ call with (MyClass instance, 'name') and {}
```
