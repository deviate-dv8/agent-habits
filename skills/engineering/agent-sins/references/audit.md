# Agent sins audit

Scan the current project for the sins listed in `../SKILL.md` and report each confirmed one with its location and fix.

## Rules

- **A grep hit is a candidate, not a finding.** Read the surrounding code before you report it. Drop false positives silently.
- **Skip** vendored code, dependency folders, build output, and generated files. Do count hand-edited generated files (see step 4).
- **Don't fix anything during the audit.** Only report.
- **Cite every finding** as `path:line`, along with the code you saw.

## Steps

1. **Learn the stack.** Read the dependency manifest(s) and lockfile. List the libraries already available for validation, dates, auth, crypto, HTTP, ORM, and so on. These are the fixes you'll recommend first.

2. **Search for candidates by domain.** Adapt the search terms to the project's languages. These are starting points, not an exhaustive list.

   | Domain | Search for |
   |---|---|
   | Validation | Regex literals near `email`, `phone`, `url`, `zip`, `postal` |
   | Regex as parser | Regex applied to HTML, XML, JSON, CSV, or URL strings |
   | Dates and time | `86400`, `3600`, `* 24 * 60`, `60 * 60 * 1000`, manual month/day math, hand-built time-zone offsets |
   | Passwords and crypto | `md5`, `sha1`, `sha256` near `password`; custom `encrypt`/`decrypt`/`sign` functions |
   | Randomness and IDs | Non-crypto random calls near `token`, `secret`, `session`, `id`, `otp` |
   | Secrets | `api_key`, `secret`, `password`, `token` assigned string literals; known key prefixes |
   | SQL | `SELECT`/`INSERT`/`UPDATE`/`DELETE` built with concatenation or string interpolation |
   | HTML escaping | Raw HTML sinks (`innerHTML` and equivalents), `.replace` calls on `<`, `>`, `&` |
   | Deserialization | `eval`, unsafe YAML or object loaders, native unserialize on input |
   | URLs and paths | Paths or URLs joined with `+ "/" +` or interpolation; `../` handling |
   | Money | Float types or float literals used with `price`, `amount`, `total`, `balance` |
   | Versions | Version strings compared as text or split on `.` |
   | Retries | `sleep` inside loops around network calls |
   | Auth | Custom token/JWT parsing, homemade session stores, manual password comparison, auth endpoints with no rate limiting |
   | Redirects | `next`/`redirect`/`returnTo` parameters checked with `startsWith` |
   | Wrappers | Access to library internals (`._`, `__`, `["~`), types that mirror a library's exported types, string-path dispatch (`split(".").reduce`), adapter files that keep an old API shape alive |

3. **Check the dependencies.**
   - Are there two libraries in the manifest for the same job?
   - Are there imports that resolve to no installed package?
   - Is the lockfile missing, or out of sync with the manifest?

4. **Check generated artifacts.**
   - If the project has a migration generator, run its drift or check command if one exists. Otherwise compare the schema against the latest migration.
   - Look for migration names the generator didn't produce, such as hand-picked round timestamps (`…0000_`) or names that break the tool's pattern.
   - Look through git history for edits to files marked as generated (headers like "generated" or "do not edit") that happened without a matching change to their source.

## Output

| # | Severity | Domain | Location | Evidence | Fix |
|---|---|---|---|---|---|
| 1 | Security | Passwords | `path:line` | what the code does | the library to use (prefer one already installed) |

Severity levels:
- **Security**: exploitable (injection, weak hashing, leaked secrets, unsafe deserialization).
- **Correctness**: wrong results on real inputs (time zones, floats, unicode, regex validation).
- **Maintainability**: duplicated or hand-rolled logic that works today but fights the codebase.

Sort by severity. Then add:
- **Counts per domain.**
- **Top 3 fixes**: the highest-impact changes, preferring libraries already installed.
- **Clean domains**: domains you checked that had no findings, so the reader knows what was covered.
