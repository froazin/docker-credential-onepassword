---
description: How to contribute to docker-credential-onepassword, and the conventions every change follows.
---

# Contributing

This section is for anyone who wants to change `docker-credential-onepassword`: fix a bug, add a feature or improve these docs. It explains how changes move from a branch to the main line, and how to write the commits and pull requests that carry them.

For a one-page summary of these conventions, see [`CONTRIBUTING.md`](https://github.com/froazin/docker-credential-onepassword/blob/main/CONTRIBUTING.md) in the repository.

## Principles

* **Trunk-based development.** All work lands on `main` through pull requests. Branches are short-lived, and there are no release branches.
* **Small, focused pull requests.** Each pull request makes one change. It's squash-merged, so it lands on `main` as a single commit and history stays linear, with no merge commits.
* **Commits are the change log.** Every commit follows [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/), with a few project extensions described in [Commit messages](commit-messages.md), so the history on `main` describes what changed in each release. A pull request's title and description become that commit's message, so write them as a commit header and body.
* **Every commit on `main` is releasable.** New or unfinished code that could change behaviour for users is gated behind a feature flag read from an environment variable, off by default.
* **AI-assisted work is credited.** Every commit that includes AI-generated code must have a `Co-Authored-By` footer.

{% hint style="info" %}
External contributors can't create branches in the main repository. Fork it, work on a branch in your fork and open a pull request from there. See [Working from a fork](git-workflow.md#working-from-a-fork).
{% endhint %}

## In this section

* [Git workflow](git-workflow.md): trunk-based development, working from a fork, rebasing and feature flags.
* [Commit messages](commit-messages.md): the commit header format, issue references, breaking changes, `[skip ci]` and the `Co-Authored-By` footer.
* [Pull requests](pull-requests.md): how to write a pull request title and description, and what happens when it's merged.
* [Versioning and releases](versioning-and-releases.md): semantic versioning, how commits change the version, Git tags and how releases from `main` will work.
