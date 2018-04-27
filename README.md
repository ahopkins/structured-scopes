# Structured Scope
Working document for defining a scope specification

## Abstract

This repository is meant to serve as a working outline of the generation of an abstract specification for the use of structured scopes in permission granting utilities.

## Introduction

The goal of this endeavor is to standardize and define the meaning of scopes. It is a work in progress and **does not currently have any license**, and may be subject to change at any time. All copyright and other rights are hereby reserved.

## Makeup of a scope

A scope is a `string` of characters encoded with `UTF-8`. It has two parts:

1. namespace
2. actions

Delimited by `:`

`null` produces exception

### Namespace

Must have either **one** or **none**.

Valid characters: any UTF-8 accepted by RFC 6749 except `:`

The first delimited part of a scope is the namespace. An undefined namespace is in the **global namespace** (eg `:` or `:someaction`)

Possible namespaces:

- `foo` = `foo`
- `:read` = Global namespace
- `:` = Global namespace
- ` ` = Empty namespace (ie, nothing can match)

### Actions

May have either **zero** or **many**.

Valid characters: any UTF-8 accepted by RFC 6749 except `:`

Actions are anything that follows the first delimited part of the scope (the namespace) and are separated by `:`

Anything that follows any empty action (which is displayed as a double color: `::`) is a negatve, for example `::exclusion`

Possible actions:

- `foo` = Top level required
- `:read:write` = Read/write actions
- `:` = Any action will work
- `foo:` = Any action will work
- `::read:write` = Any action except read and write

## Accepting a scope

### General

Required scope v. Requested scope

### Required scope

### Requested scope

## Examples of scopes

- `admin`
- `user:read`
- `user:read:write`
- `:read`
- `:read:write`
- ` `
- `:`
- `::`
- `user:write:delete::read`
