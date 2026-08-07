# Security Policy

## Supported Versions

Security fixes are applied to the current `main` branch. Version-specific
backports are not promised.

## Security Boundary

`no-staging` is an instruction-based Agent Skill. It is not a Git hook,
sandbox, permission system, or access-control boundary.

Do not rely on this plugin as the sole control protecting a Git index or
repository. A user, another process, an IDE, another agent, or a tool with
unexpected side effects may still modify the index.

An agent staging a file despite the active skill is normally a functional bug.
Treat it as a security issue only when it creates a concrete security impact,
such as exposing sensitive data or enabling an unauthorized repository change.

## Reporting a Vulnerability

Use GitHub's private vulnerability reporting feature on the repository's
**Security** tab when it is available.

Include:

- a description of the issue and its security impact;
- the affected plugin version, skill version, or commit;
- the host and environment in which it occurred;
- minimal reproduction steps; and
- any suggested mitigation.

Remove credentials, private repository contents, personal information, and
other secrets from transcripts or examples.

Do not disclose a vulnerability in a public issue before a fix or mitigation
is available. If private vulnerability reporting is unavailable, open a
minimal public issue requesting a private contact method without including
technical details.

Reports are reviewed on a best-effort basis. No fixed response or remediation
time is guaranteed.

For behavior problems without a security impact, use the repository's regular
issue tracker.
