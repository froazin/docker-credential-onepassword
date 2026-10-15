---
description: >-
  How to write commit messages for this project. Conventional Commits with
  issue references, skip ci, breaking changes and an AI co-author footer.
---

# Commit messages

Commits are the change log for this project. Every commit that lands on `main` describes one focused change, so the commit history tells users and contributors what changed and why.

We always use [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/), with one addition in the first line: optional bracketed fields between the type and the scope. They hold an issue reference, a `[skip ci]` marker, or GitBook's change request ID. These bracket fields are a project extension. They aren't part of the Conventional Commits spec.

Follow these rules for every commit you make, including commits on your own branch.

Pull requests are squash-merged, so the commit that lands on `main` is built from your pull request's title and description. Write those in the same format. See [Pull requests](pull-requests.md).

## Message structure

A commit message has a header (the first line), an optional body, and optional footers. Separate each part with one blank line.

```text
type[ref](scope)!: Description

<body>

<footer-token>: <value>
```

Here's a typical commit:

```text
feat[#123](logging): Add a timestamp to log messages

A longer description of the change.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

## Header format

The header has this shape:

```text
type[ref](scope)!: Description
```

| Part | Required | What it is | Example |
| --- | --- | --- | --- |
| `type` | Yes | The kind of change. Lowercase only. | `feat`, `fix`, `refactor`, `chore` |
| `[ref]` | No | One or two bracketed fields: a GitHub issue reference, `[skip ci]`, or both as `[#123][skip ci]`. Goes straight after the type, before the scope. See [Combining reference fields](#combining-reference-fields). | `[#123]`, `[close #123]`, `[skip ci]` |
| `(scope)` | No, but recommended | The area of the project the change affects. Lowercase only. | `(logging)`, `(ipc)`, `(docs)` |
| `!` | Only for breaking changes | Marks a breaking change. Goes straight before the colon. | `feat(sdk)!:` |
| `: Description` | Yes | A colon, one space, then a short summary of the change. | `: Add a timestamp to log messages` |

### Type

The type says what kind of change the commit makes. Conventional Commits defines two types:

* `feat`: a new feature.
* `fix`: a bug fix.

Conventional Commits also allows other types. This project uses types such as `refactor` and `chore` as well.

What the commit changes decides the version bump, not its type. For example, a `feat(docs):` commit that doesn't touch the source code doesn't bump the version. See [How commits change the version](versioning-and-releases.md#how-commits-change-the-version).

### Scope

The scope names the part of the project that the change affects, such as `logging`, `ipc`, `sdk`, `read` or `docs`. Conventional Commits makes the scope optional. Include one whenever the change has a clear area.

### Description

The description is a short summary of the change. The examples on this page write it as an instruction, for example "Add a timestamp to log messages" or "Fix typo in readme".

### Combining reference fields

If a header needs both an issue reference and `[skip ci]`, put the issue reference first. Both go between the type and the scope:

```text
chore[#123][skip ci](docs): Fix typo in readme
```

## Case rules

* The type and the scope are strictly lowercase.
* Everything else can use uppercase and lowercase letters, digits, and the symbols that Conventional Commits allows.
* You can also use `#` where you need it for GitHub issue and pull request IDs.

So `Feat` and `(Logging)` aren't allowed, but `[GITBOOK-12]`, `[#123]` and a description that starts with a capital letter are.

## Body

The body is optional. Use it to explain what changed and why, in more detail than the header allows. Start it after one blank line below the header.

```text
fix[close #123](ipc): Bounds check pw input string

Perform an initial bounds check on piped inputs thereby preventing buffer overflow attacks as per CVE-12345.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

## Issue references

If a commit relates to a GitHub issue, put the issue ID in square brackets between the type and the scope. Issue references are optional, because not every commit relates to an issue.

GitHub turns `#123` into a link to issue 123. See [Autolinked references and URLs](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls#issues-and-pull-requests) in the GitHub docs.

### Contributing to an issue

If a commit contributes to an issue without resolving it, mention the issue ID without a closing keyword. This is useful when several commits contribute to the same issue.

```text
feat[#123](logging): Add a timestamp to log messages

A longer description of the change.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

### Closing an issue

If a commit resolves an issue, put a closing keyword before the issue ID:

```text
fix[close #123](ipc): Bounds check pw input string

Perform an initial bounds check on piped inputs thereby preventing buffer overflow attacks as per CVE-12345.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

GitHub recognises these closing keywords:

* `close`, `closes`, `closed`
* `fix`, `fixes`, `fixed`
* `resolve`, `resolves`, `resolved`

GitHub closes the issue when the commit with the keyword lands on the default branch, `main`. See [Linking a pull request to an issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue) in the GitHub docs.

{% hint style="info" %}
Only use a closing keyword when the change fully resolves the issue. If the issue still needs more work, use the plain `[#123]` form so that GitHub doesn't close it.
{% endhint %}

## Skipping CI

In special cases, such as fixing a typo in the docs, you can tell GitHub not to run CI workflows for a commit. Add `[skip ci]` to the header, in square brackets before the scope:

```text
chore[skip ci](docs): Fix typo in readme

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

GitHub accepts several forms of this marker, but this project uses `[skip ci]` only. See [Skipping workflow runs](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/skip-workflow-runs) in the GitHub docs.

The repository doesn't have CI workflows yet. This rule applies once they're added.

{% hint style="warning" %}
Don't use `[skip ci]` on a commit that should bump the version. Because the pull request title becomes the commit header on `main`, `[skip ci]` also skips the workflows that run on `main` for that commit. Once automatic releases from `main` are set up, as planned, that includes the release. If you think your change is an exception, raise it in the pull request and the reviewers will decide.

When workflows are skipped, any checks that are required to pass stay pending, and that can stop a pull request from being merged.
{% endhint %}

## Breaking changes

A breaking change is a change that isn't backward compatible with the existing API. A breaking change must include **both** of these:

* A `!` straight before the colon in the header.
* A `BREAKING CHANGE:` footer that explains why the change isn't backward compatible.

```text
feat(sdk)!: Stream encoded json in read command response

Longer change description

BREAKING CHANGE: Why this change is not API backward compatible.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

Write the `BREAKING CHANGE` footer token in uppercase, as Conventional Commits requires.

From `v1.0.0`, a breaking change leads to a new major version. While the version is `0.x`, it leads to a new minor version instead. See [Before the stable release](versioning-and-releases.md#before-the-stable-release).

## AI co-author footer

If an AI agent collaborated on the code in a commit, add a `Co-Authored-By` footer that names the agent. This is mandatory for every commit that includes AI-generated code.

```text
Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

Put it in the footer, after any `BREAKING CHANGE` footer, as in the [breaking change example](#breaking-changes).

For a pull request, put the footer at the end of the pull request description, so that it's carried into the squashed commit on `main`. See [Pull requests](pull-requests.md).

## Commits made by tools

Some commits on `main` are made by tools, not by people. You don't write these by hand.

### GitBook commits

The documentation in `docs/` is a GitBook space that's synced with this repository. When a change request is merged in GitBook, GitBook commits the change to the repository in this format:

```text
chore[GITBOOK-{change_request_number}](docs): {change_request_subject}
```

For example, for change request 12:

```text
chore[GITBOOK-12](docs): Update the overview page
```

The `[GITBOOK-12]` field takes the place of an issue reference. Uppercase letters are allowed there, as described in [Case rules](#case-rules).

GitBook commits only change the docs, so they don't change the version.

### Dependabot commits

Dependabot checks the project's Go modules for updates every week. It groups the weekly version updates into one pull request, with a title and commit message that start with `chore(deps):`. For example:

```text
chore(deps): bump the gomod group with 2 updates
```

Dependabot writes the rest of the message. Dependency updates that change the build artifact are patch-level changes. See [Versioning and releases](versioning-and-releases.md).

## How a commit lands on main

Because pull requests are squash-merged, the pull request title becomes the header of the commit on `main`, and the pull request description becomes its body and footers. GitHub adds the pull request number to the end of the header when it merges, so don't type it yourself.

For example, a pull request titled `feat[resolve #123](read): Implement the read credential helper interface` lands on `main` as:

```text
feat[resolve #123](read): Implement the read credential helper interface (#321)

broader description of what has changed on this branch.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

Here `#321` is the pull request ID, and `[resolve #123]` closes issue 123. For the full pull request rules, see [Pull requests](pull-requests.md).

## Checklist

Before you commit, check that:

* The header follows `type[ref](scope)!: Description`.
* The type and scope are lowercase.
* An issue reference uses a closing keyword only if the commit resolves the issue.
* `[skip ci]` is used only in special cases, after any issue reference, and not on a commit that bumps the version unless reviewers agree to an exception.
* A breaking change has both a `!` and a `BREAKING CHANGE:` footer.
* A commit with AI-generated code has a `Co-Authored-By` footer.
