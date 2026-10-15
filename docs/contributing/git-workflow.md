---
description: >-
  How changes reach main: trunk-based development, short-lived branches,
  working from a fork, rebasing and feature flags.
---

# Git workflow

This project uses trunk-based development. `main` is the trunk. Every change you make reaches `main` through a small, focused pull request from a short-lived branch.

Working like this means each commit that lands on `main` has high value, and the commit history on `main` is the project's change log.

## The rules at a glance

* Branch from `main` and keep the branch short-lived.
* Keep each pull request small and focused on one change.
* If you're an external contributor, work in your own fork and open your pull request from it.
* Keep your branch current by rebasing on `main`. Don't merge `main` into your branch.
* Put new or unfinished behaviour behind a feature flag so `main` is always safe to release.
* Pull requests are squash-merged. There are no merge commits on `main`.

## Trunk-based development

The repository requires your changes to go through a pull request. You can't push to `main` directly.

1. Create a branch from the latest `main`.
2. Make one focused change on it.
3. Open a pull request as soon as the change is ready.

Keep branches short-lived. The longer a branch lives, the further it drifts from `main` and the harder it is to review and rebase. If a change is too big for one small pull request, split it into several. Each part can land on `main` on its own, with unfinished behaviour hidden behind a [feature flag](#feature-flags).

### No merge commits

Pull requests are squash-merged. All the commits on your branch become one commit on `main`, and the history stays linear with no merge commits.

Because the pull request becomes a single commit, its title and description become that commit's message. See [Pull requests](pull-requests.md) for how to write them and [Commit messages](commit-messages.md) for the message format.

## Working from a fork

External contributors must fork the repository. Do your work in your fork, then open a pull request from your fork to `main` in the main repository. External contributors aren't permitted to create branches in the main repository.

{% hint style="info" %}
The branch naming rules in [`CONTRIBUTING.md`](https://github.com/froazin/docker-credential-onepassword/blob/main/CONTRIBUTING.md#branch-names) apply only to branches in the main repository.
{% endhint %}

1.  Fork the repository on GitHub, then clone your fork:

    ```bash
    git clone https://github.com/<your-username>/docker-credential-onepassword.git
    cd docker-credential-onepassword
    ```
2.  Add the main repository as a remote called `upstream`:

    ```bash
    git remote add upstream https://github.com/froazin/docker-credential-onepassword.git
    ```
3.  Create a branch from the latest `main`:

    ```bash
    git fetch upstream
    git switch -c add-timestamp-to-logs upstream/main
    ```
4.  Commit your change and push the branch to your fork:

    ```bash
    git push -u origin add-timestamp-to-logs
    ```
5.  On GitHub, open a pull request from the branch in your fork to `main` in the main repository. Write its title and description as described in [Pull requests](pull-requests.md).

## Keeping your branch current

When `main` moves on while your branch is open, rebase your branch on `main`. Don't merge `main` into your branch, because that adds merge commits.

If you work in a fork, `main` is on the `upstream` remote:

```bash
git fetch upstream
git rebase upstream/main
```

If you have a branch in the main repository, use `origin` instead:

```bash
git fetch origin
git rebase origin/main
```

Rebasing rewrites your branch's commits, so you need to force-push the branch afterwards. Use `--force-with-lease`, which refuses to overwrite commits on the remote that you haven't fetched:

```bash
git push --force-with-lease
```

{% hint style="warning" %}
Only rebase and force-push your own branch. Never force-push to `main`.
{% endhint %}

## Feature flags

Every commit that lands on `main` must be releasable. When a commit that bumps the version lands on `main`, it will be released automatically (this is planned; there's no release automation yet).

So that new or unfinished code doesn't change the experience for users, put it behind a feature flag. Feature flags in this project are read from environment variables. A flag is off by default, so the new behaviour only runs when someone turns the flag on.

Use a feature flag when your change adds new behaviour, or behaviour that isn't finished yet, that could change the experience for users.

This lets you split a large change into several small pull requests, each of which is safe to release.

New code behind a flag that's off is a patch version bump, even when the commit type is `feat`. The commit that turns the flag on is a minor bump if the hidden code is a feature, or a patch bump if it's a non-breaking fix. Before `v1.0.0`, a breaking change behind a flag is a patch while it's hidden, and the commit that turns it on is a minor bump. From `v1.0.0`, breaking changes must not be hidden behind a feature flag. See [How commits change the version](versioning-and-releases.md#how-commits-change-the-version).

## Related pages

* [Commit messages](commit-messages.md): the commit message format.
* [Pull requests](pull-requests.md): pull request titles, descriptions and merging.
* [Versioning and releases](versioning-and-releases.md): version numbers, tags and releases.
