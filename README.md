# heroku-buildpack-bun

Heroku buildpack for [Bun](https://bun.sh).

Fork of [jetthoughts/heroku-buildpack-bun](https://github.com/jetthoughts/heroku-buildpack-bun),
which is itself copied from [chibat/heroku-buildpack-deno](https://github.com/chibat/heroku-buildpack-deno).

## Why this fork

Upstream doesn't support pinning a specific Bun version — it always installs
the latest and runs `bun upgrade`. This fork reads `.bun-version` and installs
that exact version, which matters when a newer Bun release has a regression
you need to avoid.

## Usage

Add the buildpack to your app:

```bash
heroku buildpacks:add https://github.com/devjah/heroku-buildpack-bun
```

Pin a Bun version by writing it to `.bun-version` in your repo root:

```
1.0.18
```

The buildpack also requires a `bun.lockb` in the repo to detect the app as a
Bun app.

On every build it runs `bun install --ignore-scripts` (arbitrary `postinstall`
scripts from transitive dependencies are not executed).

## Supported stacks

Stack-agnostic — tested on `heroku-22`. Should work on `heroku-24` (Bun
requires glibc ≥ 2.28; heroku-24 ships glibc 2.39).

## Caveats

- Heroku's Linux kernel doesn't support `io_uring`, which Bun uses for
  `Bun.write()`. Use `node:fs.writeFile()` instead.
- Switching `.bun-version` wipes the cached Bun binary and reinstalls, so the
  first build after a version bump is slightly slower.
