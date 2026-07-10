# Vendored runtime dependencies

Fundus bundles `ruamel.yaml==0.19.1` so the skill and MCP server run from the exact built plugin without a package-install step.

- Source: <https://pypi.org/project/ruamel.yaml/0.19.1/>
- Artifact: `ruamel_yaml-0.19.1-py3-none-any.whl`
- License: MIT; the upstream license is retained at `ruamel_yaml-0.19.1.dist-info/licenses/LICENSE`.
- Included modules: the pure-Python `ruamel.yaml` runtime only; no compiled extension is required.

Update the pin, bundled files, license, package checks, and frontmatter compatibility tests together.
