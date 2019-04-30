# Structured Scopes

[View the documentation](https://ahopkins.github.io/structured-scopes/) to learn more.

## Abstract

This is an abstract specification for the use of structured scopes in permission granting utilities.

## Introduction

The goal of this endeavor is to standardize and define the meaning, and usage of "scopes" for implementation in an authorization utility. It is [licensed](https://github.com/ahopkins/structured-scopes/blob/master/LICENSE) under the [CC0 1.0 Universal (CC0 1.0)
Public Domain Dedication](https://creativecommons.org/publicdomain/zero/1.0/) All other copyrights and other rights, if any, are hereby reserved.

## Purpose

The purpose of "scoping" is to provide a pass/fail response to a request for permission on a defined resource to authorized clients having the requisite permission level. A common application would be for permissioning on protected resources, for example, on web requests.

## Brief

Examples in brief:

```
Base:       user
Inbound:    foobar
Result:     fail


Base:       user
Inbound:    user
Result:     pass


Base:       user:write
Inbound:    user
Result:     pass


Base:       user:write
Inbound:    user:read
Result:     fail
```

See [more examples](https://ahopkins.github.io/structured-scopes/examples/)


## Implementations

- Python - [source](https://github.com/ahopkins/sscopes-python) | [docs](https://ahopkins.github.io/structured-scopes/implementations/)
- Javascript - [source](https://github.com/ahopkins/sscopes-javascript) | [docs](https://ahopkins.github.io/structured-scopes/implementations/)

