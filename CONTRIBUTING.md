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

## Testing Changes

Test behavioral changes in a disposable Git repository. Confirm that:

- the agent can create, edit, move, and delete working-tree files;
- the agent does not stage changes, even temporarily;
- staged changes that existed before the test remain unchanged;
- the agent avoids commands that intentionally modify the index;
- the agent asks for explicit permission when staging is required; and
- the final report distinguishes the agent's actions from pre-existing or
  external staged changes.

Validate the manifests and skill:

```console
python3 -m json.tool .codex-plugin/plugin.json
python3 -m json.tool .agents/plugins/marketplace.json
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/no-staging
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py .
git diff --check
```

The last two Python commands assume that the Codex system skills are installed
in their default location.

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
