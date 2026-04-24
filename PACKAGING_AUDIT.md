# python3-mcp Debian Packaging Audit

Audit date: 2026-04-24

Workspace:

- upstream repo: `https://github.com/modelcontextprotocol/python-sdk`
- audited release/tag: `v1.27.0`
- local packaging branch: `debian/packaging`

## Upstream package identity

- project name: `mcp`
- intended Debian binary package name: `python3-mcp`
- upstream homepage: <https://modelcontextprotocol.io>
- upstream repository: <https://github.com/modelcontextprotocol/python-sdk>
- upstream package index: <https://pypi.org/project/mcp/>
- upstream license: MIT
- Python requirement: `>=3.10`

## Build system

Upstream `pyproject.toml` at `v1.27.0` declares:

- build backend: `hatchling.build`
- build requirements:
  - `hatchling`
  - `uv-dynamic-versioning`

The upstream project uses dynamic versioning from VCS tags:

- `dynamic = ["version"]`
- `[tool.hatch.version] source = "uv-dynamic-versioning"`

## Runtime dependencies declared by upstream

Required runtime dependencies in `v1.27.0`:

- `anyio>=4.5`
- `httpx>=0.27.1`
- `httpx-sse>=0.4`
- `pydantic>=2.11.0,<3.0.0`
- `starlette>=0.27`
- `python-multipart>=0.0.9`
- `sse-starlette>=1.6.1`
- `pydantic-settings>=2.5.2`
- `uvicorn>=0.31.1` on non-Emscripten
- `jsonschema>=4.20.0`
- `pyjwt[crypto]>=2.10.1`
- `typing-extensions>=4.9.0`
- `typing-inspection>=0.4.1`
- `pywin32>=310` on Windows only

Optional extras:

- `rich>=13.9.4`
- `cli`: `typer>=0.16.0`, `python-dotenv>=1.0.0`
- `ws`: `websockets>=15.0.1`

## Debian dependency availability check

The following dependencies are confirmed in official Debian package indices:

- `python3-anyio` in trixie: 4.8.0-3
- `python3-httpx` in trixie: 0.28.1-1
- `python3-httpx-sse` in trixie: 0.4.0-3
- `python3-pydantic` in trixie: 2.10.6-2
- `python3-starlette` in trixie: 0.46.1-3
- `python3-multipart` in trixie: 1.2.1-2+deb13u1
- `python3-sse-starlette` in trixie: 2.3.4-1
- `python3-pydantic-settings` in trixie: 2.8.1-2
- `python3-uvicorn` in trixie: 0.32.0-3
- `python3-jsonschema` in trixie: 4.19.2-6
- `python3-jwt` in trixie: 2.10.1-2
- `python3-typing-extensions` is present in trixie as a common Python dependency
- `python3-typer` in trixie: 0.15.2-1
- `python3-websockets` in trixie: 15.0.1-1
- `python3-hatchling` in trixie: 1.27.0-1
- `pybuild-plugin-pyproject` in trixie

## Likely blockers

### 1. `typing-inspection`

Upstream requires `typing-inspection>=0.4.1`.

What is confirmed:

- `python3-typing-inspection` exists in Debian `forky` and `sid`
- I did not confirm `python3-typing-inspection` in Debian `trixie`

Implication:

- for a Parrot base aligned with current Debian stable, this may still need backporting or packaging, unless Parrot already carries it separately

### 2. `uv-dynamic-versioning`

Upstream build metadata requires `uv-dynamic-versioning`, but I did not find a Debian package for:

- `python3-uv-dynamic-versioning`
- `uv-dynamic-versioning`

Implication:

- packaging `python3-mcp` directly from the upstream Git checkout will not be Debian-ready without addressing the versioning plugin

## Recommended Debian packaging approach

The most realistic approach is:

1. package from the released `v1.27.0` source
2. use `pybuild` with `pybuild-plugin-pyproject`
3. patch upstream `pyproject.toml` for Debian builds so the version is static instead of VCS-derived

That avoids making `uv-dynamic-versioning` an immediate prerequisite for `python3-mcp` if maintainers prefer a small patch over another dependency package.

In practical terms, Debian packaging will likely need to:

- replace the dynamic version declaration with a static version for the packaged release
- remove `uv-dynamic-versioning` from `build-system.requires`
- keep `hatchling` as the backend

Alternative approach:

- package `python3-uv-dynamic-versioning` first

That is cleaner in terms of upstream fidelity, but it creates another packaging dependency chain.

## Initial maintainer conclusion

`python3-mcp` looks packageable for Debian or Parrot, but it is not a one-file packaging exercise.

The dependency story is materially better than expected because many runtime requirements are already in Debian stable. The two packaging risks that still need deliberate handling are:

- `typing-inspection` availability in the target archive
- upstream reliance on `uv-dynamic-versioning` during builds

## Immediate next steps

1. confirm whether Parrot already ships `python3-typing-inspection`
2. decide whether to:
   - patch out `uv-dynamic-versioning` for Debian builds, or
   - package `python3-uv-dynamic-versioning`
3. create Debian packaging skeleton:
   - `debian/control`
   - `debian/changelog`
   - `debian/rules`
   - `debian/copyright`
4. test build on Debian or Parrot:
   - `dpkg-buildpackage -us -uc`
   - `lintian`
   - `autopkgtest`

## Primary sources used

- PyPI project page: <https://pypi.org/project/mcp/>
- upstream repository: <https://github.com/modelcontextprotocol/python-sdk>
- Debian packages index for dependency availability:
  - <https://packages.debian.org/trixie/python3-anyio>
  - <https://packages.debian.org/trixie/python3-httpx>
  - <https://packages.debian.org/trixie/python3-httpx-sse>
  - <https://packages.debian.org/trixie/python3-pydantic>
  - <https://packages.debian.org/trixie/python3-starlette>
  - <https://packages.debian.org/trixie/python3-multipart>
  - <https://packages.debian.org/trixie/python3-sse-starlette>
  - <https://packages.debian.org/trixie/python3-pydantic-settings>
  - <https://packages.debian.org/trixie/python3-uvicorn>
  - <https://packages.debian.org/trixie/python3-jwt>
  - <https://packages.debian.org/trixie/python3-typer>
  - <https://packages.debian.org/trixie/python3-websockets>
  - <https://packages.debian.org/trixie/python3-hatchling>
  - <https://packages.debian.org/trixie/pybuild-plugin-pyproject>
  - <https://packages.debian.org/sid/python3-typing-inspection>
