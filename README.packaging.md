# python3-mcp Packaging Status

This repository contains Debian packaging work for the official MCP Python SDK
as `python3-mcp`.

## Current state

Implemented locally:

- initial `debian/` packaging skeleton
- static-version patch for Debian builds
- watch file and autopkgtest smoke coverage
- packaging audit and dependency review

Confirmed locally:

- patched source builds with `python -m build`
- result: `mcp-1.27.0.tar.gz`
- result: `mcp-1.27.0-py3-none-any.whl`

## Remaining blockers

- `python3-typing-inspection` availability in the target archive
- Debian or Parrot native validation still needed:
  - `dpkg-buildpackage -us -uc`
  - `lintian`
  - `autopkgtest`

## Review links

- GitHub repository: <https://github.com/Mutasem-mk4/python3-mcp-debian>
- GitLab repository: <https://gitlab.com/kharma.mutasem/python3-mcp-debian>
- GitLab MR: <https://gitlab.com/kharma.mutasem/python3-mcp-debian/-/merge_requests/1>
