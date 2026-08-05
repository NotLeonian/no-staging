# no-staging

[Japanese](README.ja.md)

`no-staging` is a Codex plugin containing an Agent Skill for Git tasks whose
changes must remain unstaged.

When the skill is active, it directs the agent to edit the working tree
normally while avoiding commands that intentionally change the Git index or
staging area.

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

To install only the skill, copy its `SKILL.md` into the local Agent Skills
directory:

```console
mkdir -p "$HOME/.agents/skills/no-staging"
cp /absolute/path/to/no-staging/skills/no-staging/SKILL.md "$HOME/.agents/skills/no-staging/SKILL.md"
```

## Usage

Invoke the skill in a Git task:

```text
$no-staging

Make the requested changes, but leave every change unstaged.
```

A compatible host may also select the skill automatically when a Git task
explicitly requires files to be added, modified, moved, or deleted without
staging them.

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

## Testing

Run the repository test suite without installing additional packages:

```console
python3 -B -m unittest discover -s tests -p 'test_*.py' -v
```

The tests validate the JSON files, cross-check the plugin and marketplace
metadata, verify skill discovery and the core `no-staging` instructions, and
check local links in the Markdown documentation. They validate the repository
structure and static instruction contract; they do not simulate an agent or
prove that an agent will follow the skill.

The GitHub Actions workflow runs the same command for pushes and pull requests.

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
│       └── SKILL.md
├── tests/
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
