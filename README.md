# `postUpgradeTasks` repro

Original discussion: https://github.com/renovatebot/renovate/discussions/13102

## Background

Lerna has a bug when working with npm@7+ ([lerna/lerna#2845](https://github.com/lerna/lerna/issues/2845)) that all indentation spaces in `package-lock.json` will be replaced with tabs. So we added a `postUpgradeTasks` config to run `prettier --write packages/**/package-lock.json` to fix this (convert tabs back to spaces).

The structure of this repro:

```
root
└─ packages
   ├─ my-foo → nanoid@3.1.30
   └─ my-bar → nanoid@3.1.29
```

This is a monorepo based on Lerna, with two packages: `my-foo` and `my-bar`. They both depend on `nanoid` with different versions. `my-foo` depends on `3.1.30` and `my-bar` depends on `3.1.29`. I configured the project to use `renovate` to upgrade only `nanoid` to latest patch version.

## Expected behavior

Renovate upgrades `nanoid` to latest patch version and only necessary changes are committed, `prettier` runs on `package-lock.json` before Renovate commits the changes.

## Actual behavior

Renovate upgrades `nanoid` to latest patch version correctly for `my-bar` but for `my-foo` the `package-lock.json` is not correctly formatted by Prettier.