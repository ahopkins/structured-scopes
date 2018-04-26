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

### Namespace

Must have either **one** or **none**.

Valid characters: any UTF-8 except `:`

### Actions

May have either **zero** or **many**.

Valid characters: any UTF-8 except `:`

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
