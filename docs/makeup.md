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
