# Structured Scope

## Abstract

This repository is meant to serve as an abstract specification for the use of structured scopes in permission granting utilities.

## Introduction

The goal of this endeavor is to standardize and define the meaning, and usage of "scopes" for implementation in an authorization utility. It is [licensed](https://github.com/ahopkins/structured-scopes/blob/master/LICENSE) under the [CC0 1.0 Universal (CC0 1.0)
Public Domain Dedication](https://creativecommons.org/publicdomain/zero/1.0/) All other copyrights and other rights, if any, are hereby reserved.

## Purpose

The purpose of "scoping" is to provide a pass/fail response to a request for permission on a defined resource to authorized clients having the requisite permission level. A common application would be for permissioning on protected resources, for example, on web requests.

## Makeup of a scope

A scope is a `string` of characters encoded with `UTF-8` consisting of characters acceptable by [RFC 6749, Section 3.3](https://tools.ietf.org/html/rfc6749#section-3.3).

A scope has two components: (1) a namespace, and (2) actions. A scope's components (including multiple actions) are delimited by a colon: `:`.

A scope cannot be a `null` value, but should produce an `invalid_scope` error, as defined by [RFC 6749, Section 4.1.2.1](https://tools.ietf.org/html/rfc6749#section-4.1.2.1).

> The requested scope is invalid, unknown, or malformed.

### Namespace

Every scope must have **zero** or **one** namespace. A namespace can either be **specific** or **global**. If a namespace is **global**, then it will be matched by any requesting scope. A **specific** namespace can only be matched by requests carrying the same specific namespace.

The absense of a namespace (as discussed below) will indicate that no match can be achieved on the namespace. This is **not** to be confused with a blank namespace, that will be inferred as being a **global namespace** (see below).

Valid characters: any UTF-8 accepted by [RFC 6749](https://tools.ietf.org/html/rfc6749#appendix-A) except a color `:`, and a space ` `.

The first delimited part of a scope is the namespace. An undefined namespace is in the **global namespace** (eg `:` or `:someaction` both produce the global namespace.) Alternatively, `global` is a protected word, and explicitly defines the namespace for a scope as the **global namespace**, example: `global:`. The **global namespace** is only available for base scopes and not for inbound scopes.

Possible namespaces:

- `foo` = `foo` as a Specific namespace
- `global` = Global namespace (explicit)
- `:read` = Global namespace (inferred)
- `:` = Global namespace (inferred)
- ` ` = Empty string, NO namespace (ie, nothing can match)

### Actions

A scope may have either **zero** or **many** actions. The actions are a narrowing focus of the namespace. A scope without any actions is said to be at the **top level**. Actions are anything that follows the first delimited part of the scope (the namespace) and are separated by `:`.

Valid characters: any UTF-8 accepted by [RFC 6749](https://tools.ietf.org/html/rfc6749#appendix-A) except a colon `:`, and a space ` `.

Anything that follows any empty action (which is displayed as a double color: `::`) is a negative action, or a negation, for example `::exclusion`. A negative action **only** impacts a specifically defined action, and has no impact upon (for example) a top level action. See below for discussion on matching. Negative actions are only permissible in base scopes, and not in inbound scopes. If an inbound scope contains a negation (`::`), then it should raise an error.

Where there are multiple actions, the order that they appear in shall have no consequence upon acceptance.

Possible actions on a scope:

- `foo` = Top level
- `:read:write` = Multiple actions, namely `read` and `write`
- `:` = Any action, wildcard
- `foo:` = Any action, wildcard
- `::read:write` = Any action except read and write

## Accepting a scope

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
    
*Note: *An implementation of this specification may offer a validation mechanism that only requires **one** action to match*

### Multiple scopes

Scopes can be chained together as a single string separated by a space:

    admin user:read ::delete

Where there are multiple scopes, the order in which they appear shall have no importance and **shall not** play a part in the acceptance of the inbound.

A validation of multiple scopes is valid if the inbound matches **all** of the base scopes. *

*Note: *An implementation of this specification may offer a validation mechanism that only requires **one** inbound to match the base*

## Examples of scopes

### Valid Scopes

- `admin`
- `user:read`
- `user:read:write`
- `:read`
- `:read:write`
- ` `
- `:`
- `::`
- `user:write:delete::read`

### Structured Scopes test cases

#### Simple single scopes - specific namespace

| Base scope | Inbound scope | Expected outcome |
|---|---|---|
|user|something|fail|
|user|user|pass|
|user|user:read|fail|
|user:read|user|pass|
|user:read|user:read|pass|
|user:read|user:write|fail|
|user:read|user:read:write|pass|
|user:read:write|user:read|fail*|
|user:read:write|user:read:write|pass|
|user:read:write|user:write:read|pass|
|user:|user|pass|
|user:|user:read|pass|
|user:|user:write|pass|

#### Simple single scopes - global namespace

| Base scope | Inbound scope | Expected outcome |
|---|---|---|
|:|:read|pass|
|:|admin|pass|
|:|*anything here*|pass|
|:read|admin|pass|
|:read|:read|pass|
|:read|:write|fail|
|global:|:read|pass|
|global:|admin|pass|
|global:|*anything here*|pass|
|global:read|admin|pass|
|global:read|:read|pass|
|global:read|:write|fail|
|global|:read|fail|
|global|admin|pass|
|user:write|global:write|fail|
|user:write|:write|fail|
|admin|global|fail|

#### Simple multiple scopes

| Base scope | Inbound scope | Expected outcome |
|---|---|---|
|user|something else|fail|
|user|something else user|pass|
|user:read|something:else user:read|pass|
|user:read|user:read something:else|pass|
|user foo|user|fail**|
|user foo|user foo|pass|
|user foo|foo user|pass|
|user:read foo|user foo|pass|
|user:read foo|user foo:read|fail|
|user:read foo|user:read foo|pass|
|user:read foo:bar|:read:bar|fail|
|user:read foo:bar|user:read foo|pass|
|user:read foo:bar|user foo|pass|
|user foo|user|fail*|

#### Complex scopes

| Base scope | Inbound scope | Expected outcome |
|---|---|---|
|::delete|user|pass|
|::delete|user:read|fail|
|::delete|user:delete|fail|
|::delete|user:read:delete|fail|
|:::delete|user|pass|
|:::delete|user:read|fail|
|:::delete|user:delete|fail|
|:::delete|user:read:delete|fail|
|user::delete|user|pass|
|user::delete|user:read|fail|
|user::delete|user:delete|fail|
|user::delete|user:read:delete|fail|
|user:::delete|user|pass|
|user:::delete|user:read|fail|
|user:::delete|user:delete|fail|
|user:::delete|user:read:delete|fail|
|user:read::delete|user|pass|
|user:read::delete|user:read|pass|
|user:read::delete|user:delete|fail|
|user:read::delete|user:read:delete|fail|
|user:read::delete|user:write|fail|
|user:read user::delete|user:read:delete|fail**|
|user:read user::delete|user:read user:delete|fail**|
| |*anything here*|fail|
|::|*anything here*|fail|
    
Note: *The preferred method of failing any scope should be `::` and not ` ` for its explicit nature.

```
Legend
* This is the default outcome. However, the validator should be capable of receiving an instruction that instead of ALL actions being required, only one must match.
** This is the default outcome. However, the validator should be capable of receiving an instruction that instead of ALL required scopes being met, only ONE required scope is fulfilled.
```

## Implementations

- [Javascript](https://github.com/ahopkins/sscopes-javascript) *IN PROGRESS*
- [Python](https://github.com/ahopkins/sscopes-python)
