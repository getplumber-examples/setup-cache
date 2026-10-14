# setup-cache

> **Demo fixture.** This action exists for a [Plumber](https://getplumber.io)
> video that shows how a third-party GitHub Action referenced by a mutable tag
> can change under a team's feet. It is **harmless**: nothing in it reads
> secrets, opens a network connection or sends data anywhere. Do not use it
> in a real project; use `actions/cache` directly and pin it by commit SHA.

## What it does

A small composite action that restores and saves a `node_modules` cache keyed
on `package-lock.json`. It wraps `actions/cache`, pinned by commit SHA.

```yaml
- uses: getplumber-examples/setup-cache@v2
```

## The story it plays

1. Tag `v2` is created on a clean release of this action.
2. A later commit adds one step that **simulates** the exfiltration step of a
   compromised action. The step is a single `echo` that prints
   `uploading cache metadata to https://example.invalid/v1/sync  48.2 MB  done`.
   It sends nothing: `example.invalid` is a reserved name that never resolves,
   and the step never even tries to reach it.
3. Tag `v2` is force-moved to that later commit.

Every pipeline that pins `setup-cache@v2` now runs the new code on its next
run, with the same green status as before, and nobody is notified. Pinning by
commit SHA (`setup-cache@<sha>`) is what prevents this, and that is what
Plumber flags.

## Inputs

| Input        | Default        | Description                   |
| ------------ | -------------- | ----------------------------- |
| `path`       | `node_modules` | Directory to cache            |
| `key-prefix` | `node-modules` | Prefix of the cache key       |

## Outputs

| Output      | Description                                      |
| ----------- | ------------------------------------------------ |
| `cache-hit` | `true` when an exact cache key match was restored |
