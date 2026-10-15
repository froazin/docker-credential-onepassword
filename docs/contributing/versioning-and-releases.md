---
description: How the project numbers its versions, tags releases and plans to deliver them from main.
---

# Versioning and releases

This page explains how versions are numbered and how releases will be made. Read it if you change the code, because every commit on `main` must be releasable.

{% hint style="warning" %}
There are no releases yet. The project has no build or CI pipeline so far. The build-time version and the automatic release from `main` described below are planned, and this page describes how they will work.
{% endhint %}

## Semantic versioning

The project uses [Semantic Versioning](https://semver.org/). A version has three numbers in the form `MAJOR.MINOR.PATCH`, for example `1.2.3`.

| Part | Example | What a change to it means |
| --- | --- | --- |
| `MAJOR` | **1**.2.3 | API breaking changes, from `v1.0.0`. See [Before the stable release](#before-the-stable-release). |
| `MINOR` | 1.**2**.3 | New features and capabilities. |
| `PATCH` | 1.2.**3** | Fixes, refactorings and dependency updates that don't materially fit into the minor or major categories. |

## How commits change the version

What a commit changes decides the version bump, not its type. Start at the top of this table and use the first row that matches:

| What the commit changes | Version bump | Example |
| --- | --- | --- |
| Nothing in the source code, whatever its type. For example docs, CI or tests. | None | `v1.4.2` stays `v1.4.2` |
| The source code, in a way that isn't backward compatible, from `v1.0.0` | Major | `v1.4.2` to `v2.0.0` |
| The source code, in a way that isn't backward compatible, while the version is `0.x` (not behind a feature flag that's off). This includes turning on a breaking change that was behind a flag. | Minor | `v0.3.1` to `v0.4.0` |
| The source code, adding a new feature or capability that users can use (not behind a feature flag that's off). This includes turning on a feature that was behind a flag. | Minor | `v1.4.2` to `v1.5.0` |
| The source code, in any other way. For example a fix, a refactoring, a dependency update that changes the build artifact, new code behind a feature flag that's off (even a breaking change at `0.x`), or turning on a non-breaking fix that was behind a flag. | Patch | `v1.4.2` to `v1.4.3` |

**Source code** is code that's reflected in the final build artifact. CI configuration, tests and docs aren't source code.

* Content wins over the commit type. A `feat(docs):` commit that doesn't touch the source code doesn't bump the version. A `feat` commit that adds new code behind a [feature flag](git-workflow.md#feature-flags) that's off is a patch. The commit that later turns the flag on is a minor bump if the hidden code is a feature, or a patch bump if it's a non-breaking fix. A hidden breaking change before `v1.0.0` follows [Before the stable release](#before-the-stable-release).
* From `v1.0.0`, a breaking change to the source code bumps the major version whatever its type, for example `fix(ipc)!:`. Before that, see [Before the stable release](#before-the-stable-release). Mark a breaking change with both a `!` before the colon and a `BREAKING CHANGE:` footer. See [Commit messages](commit-messages.md#breaking-changes) for the full rules and an example.
* Dependency updates, such as the weekly Go module updates that Dependabot opens, are patch-level changes when they change the build artifact. An update that only affects tests or tooling doesn't change the version.
* Docs changes, including the commits that GitBook makes, don't change the version.

### Before the stable release

The first version will be `v0.1.0`. While the version is `0.x`, the project isn't considered stable, and a breaking change to the source code bumps the minor version, whatever its type. For example, a `fix(ipc)!:` commit at `v0.3.1` gives `v0.4.0`. Still mark it with a `!` and a `BREAKING CHANGE:` footer.

A breaking change that's hidden behind a feature flag that's off is a patch while it's hidden. The commit that turns the flag on is a minor bump.

The project will move to `v1.0.0` when the maintainers decide it's ready. How that will happen hasn't been decided yet.

From `v1.0.0`, the project is considered stable:

* Every breaking change to the source code, including a breaking fix, bumps the major version.
* Breaking changes must not be hidden behind a feature flag.
* If the maintainers ever decide that a breaking change is absolutely necessary, they'll make a plan for it at the time.

## Git tags

Each release is tagged in Git with its version number and a `v` prefix, for example `v1.0.2` or `v2.4.2`. Always include the `v`. The first release will be tagged `v0.1.0`. The build will read this tag to set the version in the binary (see below).

## Major versions and the Go module path

This module has a `go.mod` file, so from `v2.0.0` upwards the `go` command only accepts a version tag if the module path ends in the matching major version suffix, such as `/v2`. Without the suffix, the tag is left out of `@latest` and version lists, and requesting it directly fails. So a commit that bumps the major version to `v2` or later must also add the suffix to the module path in `go.mod`, and to every import path that uses it:

```text
module github.com/froazin/docker-credential-onepassword/v2
```

Reviewers check this in the pull request.

## The version in the binary (planned)

The build will read the Git tag of the commit it's building and pass the version to the Go linker as a linker flag. This sets the version in the built binary, so the `--version` argument prints the correct version. There is no build yet, so `--version` doesn't exist today.

## Continuous delivery from main (planned)

The project will use continuous delivery from the main line. When a commit that bumps the version lands on `main`, it will be released automatically. A commit that doesn't bump the version, such as a docs update, doesn't produce a new release.

This means:

* There are no release branches. Releases will be made from `main`.
* Every commit on `main` must be ready to release. Keep pull requests small and focused, so each one lands as a single commit of high value. See [Pull requests](pull-requests.md).
* The commits on `main` are the change log. Each pull request is squash-merged into one commit, and its message comes from the pull request title and description.

## Feature flags protect releases

Because every commit on `main` must be releasable, new or unfinished code can't wait on a branch until it's done. Instead, it ships behind a feature flag.

Feature flags are read from environment variables. Code that is new or unfinished, and that could change behaviour for users, stays off by default. Users only see it when the flag is turned on. This makes sure that releases don't break the experience for the end user.

See [Git workflow](git-workflow.md#feature-flags) for how feature flags fit into trunk-based development.
