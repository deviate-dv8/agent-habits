# agent-sins

A list of things a coding agent must **not** write by hand.

Agents love to write their own regex for email validation, their own date math, or their own login flow. The result works in the demo and breaks in production, or opens a security hole. This skill lists those domains and tells the agent to use a library instead.

## Domains covered

Validation, generated files (migrations, lockfiles, API clients), regex used as a parser, dates and time, authentication, passwords, cryptography, randomness and IDs, secrets, web security plumbing, untrusted deserialization, file uploads, human data (names, addresses, phone numbers), money, SQL, HTML escaping, URLs and paths, data formats, i18n, unicode, versions, retries, concurrency, deep clone and equality, and CLI and config parsing. It also covers wrapper sins (homemade layers around a library instead of using it as designed) and dependency sins, such as packages that don't exist or are unmaintained.

## Usage

The agent loads it on its own before writing any helper, regex, or parser. To apply it explicitly, mention it:

```
Use agent-sins. Add email and birth date validation to the signup form.
```

To check an existing project for sins:

```
Audit this repo with agent-sins.
```

You get a table of confirmed sins with file locations, severity (security, correctness, or maintainability), and the library to fix each one, preferring libraries the project already has.

To add a sin, add a row to the table in `SKILL.md` that names the general domain, not one incident.
