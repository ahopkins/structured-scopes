There are currently two known implementations of Structured Scopes

- Python - [source](https://github.com/ahopkins/sscopes-python)
- Javascript - [source](https://github.com/ahopkins/sscopes-javascript)

Both of them have a very similar API.

## Installation

To get started install from your language's package manager.

```Bash  tab="Python"
pip install sscopes
```

```Bash  tab="Javascript"
npm install sscopes
```


## Acceptance

The package has a single callable function that processes acceptance and returns a `BOOLEAN` value: `validate()`.


```python tab="Python"
from sscopes import validate

is_valid = validate("user", "user")
print(is_valid)
# True
```

```javascript tab="Javascript"
import validate from 'sscopes';

const isValid = validate("user", "user");
console.log(isValid);
# true
```

## Parameters

The `validate` function accepts two **required** parameters, and **three** optional.

- `base_scopes` - **REQUIRED** The base scopes to be tested against
- `inbounds` - **REQUIRED** The inbound scopes to be accepted or rejected
- `require_all` - Whether all base scopes must be present, or just one, default `TRUE`
- `require_all_actions` - Whether all actions must be present, or just one, default `TRUE`
- `override` - A callable that will override the acceptance algorithm


```python tab="Python"
from sscopes import validate

is_valid = validate("user foobar", "user")
print(is_valid)
# False

is_valid = validate("user foobar", "user", require_all=False)
print(is_valid)
# True

is_valid = validate("user:write:read", "user:write")
print(is_valid)
# False

is_valid = validate("user:write:read", "user:write", require_all_actions=False)
print(is_valid)
# True
```

```javascript tab="Javascript"
import validate from 'sscopes';

const isValid = validate("user foobar", "user");
console.log(isValid);
# false

const isValid = validate("user foobar", "user", false);
console.log(isValid);
# true

const isValid = validate("user:write:read", "user:write");
console.log(isValid);
# false

const isValid = validate("user:write:read", "user:write", true, false);
console.log(isValid);
# true
```


## Override/Extend

Somethimes, you may decide that the predefined logic of Structured Scopes is not enough, and you need to extend it. No problem, simple pass `validate()` a callable function. Note that the callable will receive the following parameters:

- `is_valid` - Whether the standard logic would accept the inbound scope or not
- `base` - The current base scope being tested
- `inbounds` - All current inbound scope(s)
- `require_all_actions` - The state of `require_all_actions` for `validate`

!!! important
    It is important to note that the override callable is called **one time** for each base scope that was passed to `validate`. Therefore, if the base scope was `one two three`, it will be called three separate times.

```python tab="Python"
from sscopes import validate

def always_true(is_valid, base,inbounds, require_all_actions):
    return True

is_valid = validate("user foobar", "user", override=always_true)
print(is_valid)
# True
```

```javascript tab="Javascript"
import validate from 'sscopes';

const alwaysTrue = (is_valid, base,inbounds, require_all_actions) => {
    return true;
}

const isValid = validate("user foobar", "user", true, true, alwaysTrue);
console.log(isValid);
# true
```
