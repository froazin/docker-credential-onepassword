# Contributing

Thanks for helping to build `docker-credential-onepassword`, the 1Password credential helper for Docker, Podman, Skopeo and Buildah.

This page is a short summary of how we work. The [Contributing section of the docs](docs/contributing/README.md) has the full rules and more examples.

## Contents

1. [Report a problem or propose a change](#report-a-problem-or-propose-a-change)
1. [Before you start](#before-you-start)
1. [Git workflow](#git-workflow)
1. [Commit messages](#commit-messages)
1. [Pull requests](#pull-requests)
1. [Versioning and releases](#versioning-and-releases)
1. [Licence](#licence)

## Report a problem or propose a change

- Open a [GitHub issue](https://github.com/froazin/docker-credential-onepassword/issues) to report a bug or suggest a feature.
- If your change relates to an issue, reference it in your commits and pull request. See [Commit messages](#commit-messages).
- Make every change through a pull request. You can't push directly to `main`.

## Before you start

- Install the Go version named in [`go.mod`](go.mod) (currently Go 1.26).
- If you're an external collaborator, fork the repository, work in your fork and open your pull request from it. You aren't permitted to create branches in the main repository. See [Working from a fork](docs/contributing/git-workflow.md#working-from-a-fork).
- If you use [direnv](https://direnv.net/), `.envrc` loads a local `.env` file when it exists. Don't commit `.env`.

## Git workflow

We use trunk-based development:

- Branches are short-lived and pull requests are small and focused.
- Every commit that lands on `main` should be high value, because the commit history is the change log.
- There are no merge commits. Keep your branch up to date by rebasing it on `main`, not by merging `main` into it.
- New or unfinished code that could change behaviour for users goes behind a feature flag. Feature flags are read from environment variables and are off by default, so every commit on `main` is safe to release. From `v1.0.0`, breaking changes must not be hidden behind a feature flag.

See [Git workflow](docs/contributing/git-workflow.md) for the details, including how to work from a fork.

### Branch names

These rules apply to branches in the main repository. External collaborators don't create branches there. They work in a fork instead.

A branch name is a type, a `/` and a slug. The slug uses lowercase letters and digits, with words separated by hyphens.

| Type | Use it for |
|---|---|
| `feature` | Anything that adds new functionality |
| `bugfix` | Anything that fixes a bug |
| `other` | Anything that isn't a feature or a bug fix |

```text
feature/docker-credential-query
```

Unlike [GitVersion](https://gitversion.net/), which inspired these names, there's no `release` branch type. Releases will be made from `main`.

## Commit messages

We always use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/), with a few project extensions. The header looks like this:

```text
type[ref](scope)!: Description
```

- `type` and `scope` are strictly lowercase. Everything else may use upper and lower case letters, digits, the symbols Conventional Commits allows, and `#` for issue and pull request IDs.
- `scope` is optional, but use one when the change has a clear area.
- `[ref]` is a project extension, not part of Conventional Commits. It's optional. Use it for an issue reference or `[skip ci]`. If you need both, put the issue reference first: `[#123][skip ci]`. GitBook's commits also use this field for their change request ID (see below).
- `!` marks a breaking change. See below.

| Bracket field | Meaning |
|---|---|
| `[#123]` | The commit contributes to issue #123 but doesn't close it |
| `[close #123]`, `[resolve #123]` | The commit closes issue #123 when it lands on `main` |
| `[skip ci]` | GitHub skips CI for the commit (the repository has no CI workflows yet). Use it only in special cases, such as a typo fix in the docs. Don't use it on a commit that bumps the version. Raise any exception in the pull request |

GitHub's closing keywords are `close`, `closes`, `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves` and `resolved`.

If AI generated any of the code in a commit, you must add a `Co-Authored-By` footer for the AI agent:

```text
feat[#123](logging): Add a timestamp to log messages

A longer description of the change.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

A breaking change needs **both** a `!` before the colon **and** a `BREAKING CHANGE:` footer. The `Co-Authored-By` footer goes after it:

```text
feat(sdk)!: Stream encoded json in read command response

Longer change description

BREAKING CHANGE: Why this change is not API backward compatible.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

Some commits are made by tools, not by people:

- GitBook makes commits when a change request is merged in GitBook. They look like `chore[GITBOOK-{change_request_number}](docs): {change_request_subject}`.
- Dependabot opens weekly grouped Go module updates with headers that start with `chore(deps):`.

See [Commit messages](docs/contributing/commit-messages.md) for every rule and more examples.

## Pull requests

Format a pull request as if it were a commit, because it becomes one:

- The **title** follows the format of a commit header.
- The **description** follows the format of a commit body, including any footers. Put `Co-Authored-By` at the end of the description.
- Pull requests are squash-merged. GitHub builds the commit message on `main` from the title and description, and adds the pull request number, such as `(#321)`, to the end of the header. Don't type it yourself.

For example, take a pull request with this title and description.

**Title:**

```text
feat[resolve #123](read): Implement the read credential helper interface
```

**Description:**

```text
broader description of what has changed on this branch.

Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>
```

When it's merged, it lands on `main` with this header:

```text
feat[resolve #123](read): Implement the read credential helper interface (#321)
```

See [Pull requests](docs/contributing/pull-requests.md) for the details.

## Versioning and releases

We use [Semantic Versioning](https://semver.org/). Versions are `MAJOR.MINOR.PATCH`, and releases are tagged in Git with a `v` prefix, such as `v1.0.2` or `v2.4.2`. The first release will be `v0.1.0`.

What a commit changes decides the version bump, not its type. Use the first row that matches:

| What the commit changes | Version bump |
|---|---|
| Nothing in the source code, whatever its type. For example docs, CI or tests | None |
| The source code, in a way that isn't backward compatible, from `v1.0.0` | Major |
| The source code, in a way that isn't backward compatible, while the version is `0.x` (not behind a feature flag that's off). This includes turning on a breaking change that was behind a flag | Minor |
| The source code, adding a new feature or capability that users can use (not behind a feature flag that's off). This includes turning on a feature that was behind a flag | Minor |
| The source code in any other way, including fixes, refactorings, dependency updates that change the build artifact, new code behind a feature flag that's off (even a breaking change at `0.x`), and turning on a non-breaking fix that was behind a flag | Patch |

- Source code is code that's reflected in the final build artifact. So a `feat(docs):` commit doesn't bump the version.
- While the version is `0.x`, a breaking change of any type, including a fix, bumps the minor version. Still mark it as a breaking change. The project will move to `v1.0.0` when the maintainers decide it's ready. How that will happen isn't decided yet.
- From `v1.0.0`, if a breaking change ever becomes absolutely necessary, the maintainers will plan it at the time.
- A commit that bumps the major version to `v2` or later must also add the suffix to the module path in `go.mod`, such as `/v2`, and to the import paths. Reviewers check this in the pull request.

The project doesn't have a build or release pipeline yet. When it does:

- The build will read the Git tag and pass it to the binary as a linker flag, so `--version` prints the correct version.
- Each commit that bumps the version will be released automatically when it lands on `main`. A commit that doesn't bump the version, such as a docs update, doesn't produce a new release.

See [Versioning and releases](docs/contributing/versioning-and-releases.md) for the details.

## Licence

The project is released under the [MIT License](LICENSE).
