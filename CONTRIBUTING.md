# Contributing

Contributions to `no-staging` are welcome.

## Scope

Keep changes focused on the plugin's purpose: directing an agent to leave Git
working-tree changes unstaged.

Do not describe the skill as a Git hook, sandbox, access-control mechanism, or
guaranteed enforcement boundary.

For substantial behavioral changes, open an issue before preparing a pull
request.

## Documentation and Language

`skills/no-staging/SKILL.md` is the authoritative skill definition.

When changing its behavior:

- update `README.md` and `README.ja.md` where necessary;
- preserve the same technical meaning in both READMEs;
- write all files except `README.ja.md` in English; and
- keep the documented limitations accurate.

Do not claim that the skill can control users, IDEs, other processes, other
agents, or Git itself.

The plugin manifest and the skill metadata have separate version scopes. Bump
the plugin version for packaging changes and the skill metadata version for
changes to its instruction contract. A change may require one or both.

## Testing Changes

Run the repository tests from the root of a Git working tree. The suite uses
Git and the Python packages pinned in `tests/requirements.txt`. Install the
packages before running the tests:

```console
python -m pip install --disable-pip-version-check -r tests/requirements.txt
python -B -m unittest discover -s tests -p 'test_*.py' -v
```

GitHub Actions runs automatically for pull requests and pushes to `main`.
Pushes to non-`main` pull request branches therefore produce only the
pull-request-triggered run. The workflow can also be dispatched manually. Each
run installs the pinned packages and runs the same suite on Windows, macOS, and
Ubuntu with Python 3.13. The suite validates the manifests, their cross-file
metadata, skill discovery, the core instruction contract, the Python check
configuration, and local documentation links. The link inventory includes
tracked and non-ignored untracked files with `.md` or `.markdown` filename
extensions throughout the working tree; extension matching is case-insensitive.
For Markdown hyperlink targets, nonempty fragments must match a generated
heading anchor or an explicit `<a name>` custom anchor. The local heading-anchor
generator implements GitHub's documented rules, but it is not a live comparison
with GitHub rendering. Markdown source-line links such as `?plain=1#L10` are
checked against the target file's line range instead. Fragments for other file
formats and browser text-fragment directives such as `#:~:text=example` are not
checked. Paths beginning with `/` are treated as local paths relative to the
repository root; use an absolute web URL for a site-root-relative route. Link
parsing uses the CommonMark preset; extensions must be enabled deliberately if
repository documentation begins to depend on them. The suite does not replace
the behavioral checks below.

The requirements file also pins Ruff, mypy, and Pyright. Run the Python checks
from the repository root after installing it:

```console
ruff format --check --config tests/pyproject.toml tests/test_repository.py
ruff check --config tests/pyproject.toml tests/test_repository.py
mypy --config-file tests/pyproject.toml tests/test_repository.py
pyright --project tests/pyproject.toml tests/test_repository.py
```

These commands use the Python 3.13 settings in `tests/pyproject.toml`. To apply
formatting, omit `--check` from the first command. The Pyright configuration
resolves installed packages from `.venv` at the repository root; install the
test packages in that environment or override the virtual-environment settings
locally when using a different environment.

Test behavioral changes in a disposable Git repository. Confirm that:

- the agent can create, edit, move, and delete working-tree files;
- the agent does not stage changes, even temporarily;
- staged changes that existed before the test remain unchanged;
- the agent avoids commands that intentionally modify the index;
- the agent asks for explicit permission when staging is required; and
- the final report distinguishes the agent's actions from pre-existing or
  external staged changes.

For additional validation in a Codex installation that includes the system
skill scripts and their Python dependencies, run:

```console
python3 -m json.tool .codex-plugin/plugin.json
python3 -m json.tool .agents/plugins/marketplace.json
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py .
git diff --check
```

The plugin validator is optional because it depends on files outside this
repository. It assumes that the Codex system skills are installed in their
default location and also checks the bundled skill manifest.

For a local Codex test, register the repository as a marketplace and install
the plugin:

```console
codex plugin marketplace add /absolute/path/to/no-staging
codex plugin add no-staging@no-staging
```

Start a new Codex thread before testing an updated installation.

## Pull Requests

A pull request should:

- explain the problem and the proposed behavior;
- identify changed limitations or compatibility assumptions;
- include documentation updates when behavior changes;
- avoid unrelated formatting or prose changes; and
- contain no generated or environment-specific files.

The skill governs an agent only while it is active. It does not prohibit human
contributors from using their normal Git workflow. If you test while the skill
is active, disable it or obtain the required permission before asking that
agent to stage or commit changes.

## License

By submitting a contribution, you agree that it may be distributed under the
[Apache License 2.0](LICENSE).
