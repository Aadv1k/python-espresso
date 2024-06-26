# Espresso ☕

A tiny, type-safe expression parser in python.

### This documentation is a WIP

## Rationale

In certain cases, there is need for a safe way for the back-end to call functions provided by the client, espresso addresses this through a type-safety post-processing setup to ensure the function always receives the type it was defined with.

## Example

```python
import espresso

import random
import string

# An interface between the interpreter frontend and backend
ctx = espresso.Context()

# Namespaces are automatically resolved. In this case, within the space
# random, integer would be defined. Functionally this is syntactic sugar,
# the namespaces are simply nested dictionaries.
ctx.define_function(
    random.randint, ["random", "integer"], ["int", "int"]
)

# Variadic typing is also supported. The method will be passed an array of
# all the arguments (of a singular type).
ctx.define_function(
    random.choice, ["random", "oneOf"], ["*any"]
)
ctx.define_function(
    random.choice, ["random", "oneOfInteger"], ["*int"]
)
ctx.define_function(
    random.choice, ["random", "oneOfString"], ["*str"]
)

def random_string(length=32):
    return "".join(
        random.choice(string.ascii_letters + string.digits) for i in range(length)
    )


# Optional typing, will pass None if not provided
ctx.define_function(
    random_string, ["random", "string"], ["str?"]
)

ctx.eval("random.integer(0, 10)")
```

## Integration and Usage

To see how espresso is integrated into a production SaaS, you can see the [YAML Resolver](https://github.com/aadv1k/project-bombay/tree/master/core/YAMLResolver.py) which defines a bunch of functions and handles errors.

```yaml
- id: random.string(32)
- first_name: person.firstName
- last_name: person.lastName
- age: random.integer(0, 32)
- plan: choices.oneOf("beta", "pro", "free")
```
