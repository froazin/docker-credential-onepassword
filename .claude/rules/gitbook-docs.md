# GitBook documentation

`docs/` is a GitBook space. Its pages are published as the project's public documentation site.

This rule loads in every session, not just when you open a matching file. That way it's already in context before your first change to `docs/`, including a new file you write or a page you move or delete from the shell.

## Load the GitBook skills before you change anything

- Before you create, edit, move or delete anything under `docs/`, load the `write-docs` skill with the Skill tool. This covers pages, `SUMMARY.md`, `.gitbook.yaml`, `.gitbook/vars.yaml`, `.gitbook/includes/` and assets.
- When the skill points you to one of its reference files for the task, read it before you write. For example, read `references/configuration.md` before you edit `SUMMARY.md` or `.gitbook.yaml`, and read `references/blocks.md` before you use a block you haven't used yet in this session.
- Before you add, change or troubleshoot an OpenAPI spec, an `{% openapi %}` block or a GitBook `x-*` extension, also load the `write-openapi` skill.
- Load each skill once per session, before the first change it applies to. You don't need to load it again for every file.

## Work through Git

- Treat the files in `docs/` as the source of truth. Edit them and commit them like any other change.
- Use the `gitbook` MCP server from `.mcp.json` when you need to read or change something in GitBook itself. Don't push page content into GitBook through a change request unless the user asks; the `write-docs` skill explains when that's appropriate.
- Never write a GitBook token into a file in this repository.

## Write for the public

- Write for people who use the credential helper. Put contributor and development guidance in `CONTRIBUTING.md`, not in `docs/`.
- Document only behaviour that exists in the code on the current branch. Describe anything planned as planned.
- Don't include real credentials, tokens, account URLs, vault names, item IDs or internal hostnames in examples. Use placeholders such as `<vault>` and `registry.example.com`.
- Keep each page's `#` heading and its link text in `SUMMARY.md` the same.
