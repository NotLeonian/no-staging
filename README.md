# no-staging

[Japanese](README.ja.md)

`no-staging` is a Codex plugin containing an explicitly invoked Agent Skill for
Git tasks whose changes must remain unstaged.

When the skill is active, it directs the agent to edit the working tree
normally while avoiding commands that intentionally change the Git index or
staging area. The skill is used only when the user explicitly invokes
`$no-staging` or directly asks to use the no-staging skill.

## Behavior

The skill directs the agent to:

- leave added, modified, moved, and deleted files unstaged;
- avoid `git add`, `git stage`, `git commit`, `git mv`, `git rm`, and any
  other command that intentionally changes the index;
- preserve staged changes that existed before the task;
- use read-only Git inspection commands when needed;
- ask for explicit permission if completing the task requires staging; and
- report the changed files at the end and state that it did not stage them.

The restriction applies even to temporary staging.

## Installation

Clone the repository, register its absolute path as a local marketplace, and
install the plugin:

```console
git clone https://github.com/NotLeonian/no-staging.git
codex plugin marketplace add /absolute/path/to/no-staging
codex plugin add no-staging@no-staging
```

Start a new Codex thread after installation so that the skill is loaded.

To install only the skill, copy its directory into the local Agent Skills
directory:

```console
mkdir -p "$HOME/.agents/skills"
cp -R /absolute/path/to/no-staging/skills/no-staging "$HOME/.agents/skills/"
```

## Usage

Invoke the skill in a Git task:

```text
$no-staging

Make the requested changes, but leave every change unstaged.
```

The skill is not selected automatically for ordinary Git tasks, requests to
leave changes unstaged, staging-related risks, or mentions of the skill.
Discussing, maintaining, installing, or configuring the skill does not invoke
it.

## Limitations

`no-staging` is an instruction for an agent, not a Git enforcement mechanism.

- It is not a Git hook, sandbox, permission system, or operating-system access
  control.
- It applies only while the host has loaded the skill and the agent follows
  it.
- It cannot prevent a user, IDE, background process, another agent, or another
  session from changing the index.
- It does not remove or unstage changes that were already staged.
- It allows files in the working tree to be created, edited, moved, or
  deleted. It does not protect working-tree content or create backups.
- It does not apply to version-control systems other than Git, and it does not
  prohibit writes unrelated to staging.
- It is not a general Git safety policy. Operations unrelated to staging
  remain subject to the host's other instructions and safeguards.
- Third-party commands may have index-changing side effects that the skill
  cannot independently block.
- A task that inherently requires staging cannot proceed until the user gives
  explicit permission.

Git commands commonly treated as read-only can still invoke FSMonitor, filters,
external diff helpers, pagers, or other configured programs. They may also
refresh index metadata without staging file content. This skill protects the
intended staging state; it does not guarantee that `.git/index` remains
byte-for-byte unchanged or that inspection commands have no side effects.
Use separate technical controls, a trusted execution policy, or a companion
such as `codex-read-only-approver` when those guarantees matter.

A final statement that the agent did not stage files describes the agent's own
actions. It does not prove that the repository contains no staged changes from
another source.

## Verification

Inspect the staging state before and after the task:

```console
git status --short
git diff --cached --name-only
```

If the repository already had staged changes, compare the output before and
after rather than expecting the second command to be empty. These commands are
inspection examples, not enforcement.

## Repository Validation

Validate the JSON files and inspect the Markdown and skill content before
publishing:

```console
python3 -m json.tool .codex-plugin/plugin.json
python3 -m json.tool .agents/plugins/marketplace.json
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py .
git diff --check
```

The plugin validator depends on Codex system skill files and their Python
dependencies. It is optional when those files are unavailable. It also checks
the bundled skill manifest.

Validation confirms the static plugin and skill structure. It does not
simulate an agent or prove that an agent will follow the skill correctly.

## Testing

Run the repository test suite from the root of a Git working tree. The suite
uses Git and the Python packages pinned in `tests/requirements.txt`. Install
the packages before running the tests:

```console
python -m pip install --disable-pip-version-check -r tests/requirements.txt
python -B -m unittest discover -s tests -p 'test_*.py' -v
```

The tests validate the JSON files, cross-check the plugin and marketplace
metadata, verify skill discovery and the core `no-staging` instructions, and
verify the Python check configuration. They check local links in tracked and
non-ignored untracked files with `.md` or `.markdown` filename extensions
throughout the working tree; extension matching is case-insensitive. For
hyperlinks to Markdown documents, the tests also verify nonempty fragments
against generated heading anchors and explicit `<a name>` custom anchors.
Heading anchors are generated locally from GitHub's documented rules for case,
whitespace, punctuation, markup, and duplicate headings. This is not a live
comparison with GitHub rendering, so future GitHub changes and uncommon Unicode
edge cases may differ. GitHub Markdown source-line links such as
`?plain=1#L10` are checked against the target file's line range instead of its
heading anchors. Fragments for other file formats and browser text-fragment
directives such as `#:~:text=example` are outside this check. Paths beginning
with `/` are treated as local paths relative to the repository root; use an
absolute web URL for a site-root-relative route.

The tests validate the repository structure and static instruction contract;
they do not simulate an agent or prove that an agent will follow the skill.
Link parsing follows the CommonMark preset provided by `markdown-it-py`;
syntax that requires a separate Markdown extension is not enabled.

The GitHub Actions workflow runs automatically for pull requests and pushes to
`main`. Pushes to non-`main` pull request branches therefore produce only the
pull-request-triggered run. The workflow can also be dispatched manually. Each
run installs the pinned packages and runs the same test suite on Windows,
macOS, and Ubuntu with Python 3.13.

The contributor-facing Ruff, mypy, and Pyright commands are documented in
[CONTRIBUTING.md](CONTRIBUTING.md). The requirements file pins those tools as
well as the packages used to parse Markdown.

## Repository Layout

```text
no-staging/
├── .github/
│   └── workflows/
│       └── ci.yml
├── .agents/
│   └── plugins/
│       └── marketplace.json
├── .codex-plugin/
│   └── plugin.json
├── skills/
│   └── no-staging/
│       ├── agents/
│       │   └── openai.yaml
│       └── SKILL.md
├── tests/
│   ├── pyproject.toml
│   ├── requirements.txt
│   └── test_repository.py
├── .editorconfig
├── .gitattributes
├── .gitignore
├── CONTRIBUTING.md
├── LICENSE
├── README.ja.md
├── README.md
└── SECURITY.md
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## Security

See [SECURITY.md](SECURITY.md) for the security policy and reporting
instructions. Do not use this skill as a security boundary.

## License

Licensed under the [Apache License 2.0](LICENSE).
