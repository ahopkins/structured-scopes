### General

The pattern for acceptance is that a client offers an "inbound" scope to an authorizing agent and requests acceptance. That authorizing agent has a predefined "base" scope. The role of the authorizing agent is to match the inbound against the base and produce a `pass`/`fail` result.

### Base

The base can be any valid scope, including negations.

### Inbounds

Inbounds **may not** contain negations (`::`). And, if they do, the authorizing agent should produce an error without providing a `pass`/`fail` result.

### How scopes are matched

An inbound shall `pass` against a given base if:

1. The namespace of the inbound is equal to the namespace of the base, except a **global** namespace matches every other namespace, and an empty namespace is **unmatchable**.
2. If the base has a defined action, or actions, then the inbound must:
    - be a top level scope with no defined actions, or
    - contain all of the same actions as the base*
3. If the base does NOT have a defined action (and therefore is top level), then the inbound must be a top level scope also.
    
!!! note
    An implementation of this specification may offer a validation mechanism that only requires **one** action to match

### Multiple scopes

Scopes can be chained together as a single string separated by a space:

    admin user:read ::delete

Where there are multiple scopes, the order in which they appear shall have no importance and **shall not** play a part in the acceptance of the inbound.

A validation of multiple scopes is valid if the inbound matches **all** of the base scopes. *

!!! note
    An implementation of this specification may offer a validation mechanism that only requires **one** inbound to match the base

