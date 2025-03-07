<div align="center">
<h1>cross-env 🔀</h1>

<p>Run scripts that set and use environment variables across platforms</p>
</div>

**🚨 NOTICE: cross-env still works well, but is in maintenance mode. No new
features will be added, only serious and common-case bugs will be fixed, and it
will only be kept up-to-date with Node.js over time.
[Learn more](https://github.com/kentcdodds/cross-env/issues/257)**

---

<!-- prettier-ignore-start -->
[![Build Status][build-badge]][build]
[![Code Coverage][coverage-badge]][coverage]
[![version][version-badge]][package]
[![downloads][downloads-badge]][npmtrends]
[![MIT License][license-badge]][license]
[![All Contributors][all-contributors-badge]](#contributors-)
[![PRs Welcome][prs-badge]][prs]
[![Code of Conduct][coc-badge]][coc]
<!-- prettier-ignore-end -->

## The problem

Most Windows command prompts will choke when you set environment variables with
`NODE_ENV=production` like that. (The exception is [Bash on Windows][win-bash],
which uses native Bash.) Similarly, there's a difference in how windows and
POSIX commands utilize environment variables. With POSIX, you use: `$ENV_VAR`
and on windows you use `%ENV_VAR%`.

## This solution

`cross-env` makes it so you can have a single command without worrying about
setting or using the environment variable properly for the platform. Just set it
like you would if it's running on a POSIX system, and `cross-env` will take care
of setting it properly.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Installation](#installation)
- [Usage](#usage)
- [`cross-env` vs `cross-env-shell`](#cross-env-vs-cross-env-shell)
- [Windows Issues](#windows-issues)
- [Inspiration](#inspiration)
- [Other Solutions](#other-solutions)
- [Issues](#issues)
  - [🐛 Bugs](#-bugs)
  - [💡 Feature Requests](#-feature-requests)
- [LICENSE](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Installation

This module is distributed via [npm][npm] which is bundled with [node][node] and
should be installed as one of your project's `devDependencies`:

```
npm install --save-dev cross-env
```

> WARNING! Make sure that when you're installing packages that you spell things
> correctly to avoid [mistakenly installing malware][malware]

> NOTE : Version 7 of cross-env only supports Node.js 10 and higher, to use it
> on Node.js 8 or lower install version 6 `npm install --save-dev cross-env@6`

## Usage

I use this in my npm scripts:

```json
{
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
  }
}
```

Ultimately, the command that is executed (using [`cross-spawn`][cross-spawn])
is:

```
webpack --config build/webpack.config.js
```

The `NODE_ENV` environment variable will be set by `cross-env`

You can set multiple environment variables at a time:

```json
{
  "scripts": {
    "build": "cross-env FIRST_ENV=one SECOND_ENV=two node ./my-program"
  }
}
```

You can also split a command into several ones, or separate the environment
variables declaration from the actual command execution. You can do it this way:

```json
{
  "scripts": {
    "parentScript": "cross-env GREET=\"Joe\" npm run childScript",
    "childScript": "cross-env-shell \"echo Hello $GREET\""
  }
}
```

Where `childScript` holds the actual command to execute and `parentScript` sets
the environment variables to use. Then instead of run the childScript you run
the parent. This is quite useful for launching the same command with different
env variables or when the environment variables are too long to have everything
in one line. It also means that you can use `$GREET` env var syntax even on
Windows which would usually require it to be `%GREET%`.

If you precede a dollar sign with an odd number of backslashes the expression
statement will not be replaced. Note that this means backslashes after the JSON
string escaping took place. `"FOO=\\$BAR"` will not be replaced.
`"FOO=\\\\$BAR"` will be replaced though.

Lastly, if you want to pass a JSON string (e.g., when using [ts-loader]), you
can do as follows:

```json
{
  "scripts": {
    "test": "cross-env TS_NODE_COMPILER_OPTIONS={\\\"module\\\":\\\"commonjs\\\"} node some_file.test.ts"
  }
}
```

Pay special attention to the **triple backslash** `(\\\)` **before** the
**double quotes** `(")` and the **absence** of **single quotes** `(')`. Both of
these conditions have to be met in order to work both on Windows and UNIX.

## `cross-env` vs `cross-env-shell`

The `cross-env` module exposes two bins: `cross-env` and `cross-env-shell`. The
first one executes commands using [`cross-spawn`][cross-spawn], while the second
one uses the `shell` option from Node's `spawn`.

The main use case for `cross-env-shell` is when you need an environment variable
to be set across an entire inline shell script, rather than just one command.

For example, if you want to have the environment variable apply to several
commands in series then you will need to wrap those in quotes and use
`cross-env-shell` instead of `cross-env`.

```json
{
  "scripts": {
    "greet": "cross-env-shell GREETING=Hi NAME=Joe \"echo $GREETING && echo $NAME\""
  }
}
```

The rule of thumb is: if you want to pass to `cross-env` a command that contains
special shell characters _that you want interpreted_, then use
`cross-env-shell`. Otherwise stick to `cross-env`.

On Windows you need to use `cross-env-shell`, if you want to handle
[signal events](https://nodejs.org/api/process.html#process_signal_events)
inside of your program. A common case for that is when you want to capture a
`SIGINT` event invoked by pressing `Ctrl + C` on the command-line interface.

## Windows Issues

Please note that `npm` uses `cmd` by default and that doesn't support command
substitution, so if you want to leverage that, then you need to update your
`.npmrc` to set the `script-shell` to powershell.
[Learn more here](https://github.com/kentcdodds/cross-env/issues/192#issuecomment-513341729).

## Inspiration

I originally created this to solve a problem I was having with my npm scripts in
[angular-formly][angular-formly]. This made contributing to the project much
easier for Windows users.

## Other Solutions

- [`env-cmd`](https://github.com/toddbluhm/env-cmd) - Reads environment
  variables from a file instead
- [`@naholyr/cross-env`](https://www.npmjs.com/package/@naholyr/cross-env) -
  `cross-env` with support for setting default values

## Issues

_Looking to contribute? Look for the [Good First Issue][good-first-issue]
label._

### 🐛 Bugs

Please file an issue for bugs, missing documentation, or unexpected behavior.

[**See Bugs**][bugs]

### 💡 Feature Requests

This project is in maintenance mode and no new feature requests will be
considered.

[**Learn more**](https://github.com/kentcdodds/cross-env/issues/257)

## LICENSE

MIT

<!-- prettier-ignore-start -->
[npm]: https://npmjs.com
[node]: https://nodejs.org
[build-badge]: https://img.shields.io/github/workflow/status/kentcdodds/cross-env/validate?logo=github&style=flat-square
[build]: https://github.com/kentcdodds/cross-env/actions?query=workflow%3Avalidate
[coverage-badge]: https://img.shields.io/codecov/c/github/kentcdodds/cross-env.svg?style=flat-square
[coverage]: https://codecov.io/github/kentcdodds/cross-env
[version-badge]: https://img.shields.io/npm/v/cross-env.svg?style=flat-square
[package]: https://www.npmjs.com/package/cross-env
[downloads-badge]: https://img.shields.io/npm/dm/cross-env.svg?style=flat-square
[npmtrends]: http://www.npmtrends.com/cross-env
[license-badge]: https://img.shields.io/npm/l/cross-env.svg?style=flat-square
[license]: https://github.com/kentcdodds/cross-env/blob/master/LICENSE
[prs-badge]: https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square
[prs]: http://makeapullrequest.com
[coc-badge]: https://img.shields.io/badge/code%20of-conduct-ff69b4.svg?style=flat-square
[coc]: https://github.com/kentcdodds/cross-env/blob/master/other/CODE_OF_CONDUCT.md
[emojis]: https://allcontributors.org/docs/en/emoji-key
[bugs]: https://github.com/kentcdodds/cross-env/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3A%22%F0%9F%90%9B+Bug%22+sort%3Acreated-desc
[good-first-issue]: https://github.com/kentcdodds/cross-env/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc+label%3A%22good+first+issue%22

[angular-formly]: https://github.com/formly-js/angular-formly
[cross-spawn]: https://www.npmjs.com/package/cross-spawn
[malware]: http://blog.npmjs.org/post/163723642530/crossenv-malware-on-the-npm-registry
[ts-loader]: https://www.npmjs.com/package/ts-loader
[win-bash]: https://msdn.microsoft.com/en-us/commandline/wsl/about
<!-- prettier-ignore-end -->
