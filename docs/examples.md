## Examples of scopes

### Valid Scopes

- `admin`
- `user:read`
- `user:read:write`
- `:read`
- `:read:write`
- `:`
- `::`
- `user:write:delete::read`
- ` ` (a blank space)

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
    
!!! note
    The preferred method of failing any scope should be `::` and not an empty space for its explicit nature.

```
Legend
* This is the default outcome. However, the validator should be capable of receiving an instruction that instead of ALL actions being required, only one must match.
** This is the default outcome. However, the validator should be capable of receiving an instruction that instead of ALL required scopes being met, only ONE required scope is fulfilled.
```
