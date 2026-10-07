---
name: agent-sins
description: Catalog of domains where coding agents must not hand-roll their own implementation and must use the project's existing library, the standard library, or a well-established package instead. Covers validation, regex parsing, dates and time zones, authentication and passwords, cryptography and randomness, money, structured-format parsing, SQL and HTML escaping, URLs and paths, i18n, unicode, retries, concurrency, and IDs. Use before writing any utility, helper, regex, parser, migration, or generated file, and whenever a task touches input validation, time, auth, security, money, or data formats. Also use when asked to audit or scan a project for agent sins, hand-rolled code, or security and library-misuse issues.
---

# Agent Sins

Some problems look like a five-line helper but are really years of edge cases, standards, and security fixes. Hand-rolling them produces code that passes the happy path and fails in production. **If a task falls into one of the domains below, a library is the default. Hand-rolling needs a stated reason.**

## Before writing any helper

1. **Name the domain.** Ask whether this is validation, time, auth, parsing, or one of the other domains below. Treat a regex or a hand-written math formula as a signal that you're probably inside one.
2. **Search, in this order:** the project's own helpers, then the libraries already in its dependency manifest, then the standard library, then a well-established package.
3. **Propose before adding a dependency.** Name the package and say why it fits. Don't add one silently.
4. **Hand-roll only with a stated reason**, for example no dependencies allowed, a trivial case truly covered, or the library doesn't fit. Put the reason in your reply, not in a comment that apologizes for the code.

## The sins

| Domain | Sin | Do instead | Why |
|---|---|---|---|
| **Input validation** | Regexes for email, URL, phone, postal codes, IDs, or hand-written field checks | The project's validation or schema library, or one well-established validator | Specs such as RFC 5322 and E.164 have edge cases a regex misses, and schemas keep validation in one place |
| **Generated artifacts** | Hand-writing or hand-editing files a tool generates: DB migrations from an ORM's schema diff, lockfiles, API clients and types from OpenAPI, GraphQL, or protobuf, ORM client code, framework scaffolds | Change the source (schema, spec, manifest) and run the project's generator command, then review its output | Hand-written output drifts from the source, skips the tool's ordering and checksums, and gets overwritten on the next generation |
| **Regex as a parser** | Parsing HTML, XML, JSON, CSV, URLs, or nested or quoted structures with regex | A real parser for that format | Regex can't handle nesting, escaping, or quoting correctly |
| **Dates and time** | Manual date math, adding `86400` seconds per day, hand-built time-zone offsets, custom date formatting or parsing | A date/time library or the standard library's time-zone-aware types | DST, leap years, time zones, and locales break naive math |
| **Authentication and sessions** | Custom login flows, session tokens, JWT signing or verification, OAuth handling | The framework's auth module or an established auth library or provider | Auth bugs are security bugs, and subtle mistakes such as timing, expiry, or algorithm confusion are exploitable |
| **Passwords** | Hashing with a general-purpose hash (MD5, SHA-*), unsalted hashes, custom comparison | A dedicated password hash (argon2, bcrypt, scrypt) through a library | General-purpose hashes are fast, which is exactly wrong for passwords |
| **Cryptography** | Inventing encryption, signing, or key derivation, or picking modes and IVs by hand | A high-level crypto library API | You can't test homemade crypto for correctness, and it fails silently |
| **Randomness and IDs** | Using non-crypto random for tokens or secrets, hand-built UUIDs, timestamp-plus-counter IDs | A secure random source and a standard UUID/ULID generator | Predictable tokens can be guessed, and homemade IDs collide |
| **Secrets and credentials** | Hardcoding API keys, passwords, or tokens in code, config, or tests | Environment variables or the project's secret manager | Committed secrets leak through git history, logs, and forks |
| **Web security plumbing** | Hand-rolled CSRF tokens, CORS logic, security headers, or rate limiting | The framework's middleware or an established security library | Each one has subtle bypasses that the library already handles |
| **Untrusted deserialization** | Loading untrusted input with unsafe deserializers (native object serialization, YAML full-load, `eval`) | Safe loaders and schema validation after parsing | Unsafe deserialization lets an attacker run code |
| **File uploads** | Trusting the file extension or client MIME type, building storage paths from the user's filename | Content-based type detection and generated storage names | Spoofed types and path traversal lead to code execution or overwritten files |
| **Human data** | Splitting names into first and last, fixed address formats, hand-parsed phone numbers | Free-form name fields, address and phone libraries (for example a libphonenumber port) | Names, addresses, and phone formats vary across cultures and countries |
| **Money and decimals** | Floating-point currency math, manual rounding | Decimal types or integer minor units, plus a money library | Floats can't represent 0.1, so totals drift |
| **SQL** | Building queries by string concatenation | Parameterized queries, a query builder, or an ORM | Concatenation leads to SQL injection |
| **HTML and output escaping** | Hand-rolled escaping or sanitizing with replace or regex | The template engine's auto-escaping or a sanitizer library | Hand-rolled escaping leads to XSS, because there are too many contexts and encodings |
| **URLs, query strings, paths** | Concatenating or splitting URLs and file paths by hand; hand-rolled open-redirect or allowlist checks with string prefixes | The standard library's URL and path APIs | Encoding, separators, and path traversal (`../`) are easy to get wrong |
| **Structured data formats** | Hand-written CSV, YAML, TOML, or INI readers and writers | A standard parser and serializer | Quoting, escaping, and multiline values are hard to get right |
| **i18n and formatting** | Manual pluralization, number, currency, or date formatting by string concatenation | The platform's i18n and locale APIs | Locales differ in separators, plural rules, and order |
| **Unicode and text** | Byte-length truncation, naive case-folding, comparison without normalization | Unicode-aware string APIs and normalization | Grapheme clusters, combining marks, and locale casing break naive code |
| **Versions** | Comparing version strings as text or by splitting on dots | A semver or version library | Pre-release tags and numeric ordering break string comparison |
| **Retries, backoff, rate limits** | Ad-hoc `sleep` loops and hand-rolled backoff | The HTTP client's retry support or a retry library | Missing jitter, caps, and idempotency handling cause outages |
| **Concurrency** | Homemade locks, flags, or polling loops for coordination | Standard-library synchronization primitives, queues, or the platform's job system | Race conditions don't show up in tests |
| **Deep clone, equality, merge** | Recursive helpers written from scratch | The standard library or the project's existing utility | Cycles, special types, and prototypes are the hard part |
| **CLI args, config, env** | Parsing `argv` or env vars by hand | An argument-parsing or config library | Help text, types, defaults, and validation come for free |

## Wrapper sins

Adopting a library and then building a homemade layer around it is the same sin in disguise. It's worst with libraries newer than the agent's training data: the agent doesn't know the API, so it rebuilds the patterns it does know on top.

- **Read the installed version's docs first.** For a library you haven't used, or one newer than your training, read its docs and type definitions before writing glue code. Don't rely on memory.
- **Check for official integrations** (plugins, adapters, framework bindings) before bridging the library to another one yourself.
- **Use it as designed.** Don't reach into private or internal fields (`_x`, `__internal`, `~x`, undocumented properties), and don't re-declare types the library already exports.
- **Migrate callers; don't bridge.** A wrapper that exists to keep the old interface (old URLs, old error shapes, old call style) alive is stale material. Move the callers to the library's API.
- **A thin wrapper is fine only for a real seam,** such as one place for config, auth headers, or defaults. If a wrapper is longer than the code that uses it, or adds its own type-level machinery, it's a sin.

## Dependency sins

Using a library is the default, but adding one carelessly is its own sin.

- **Verify the package exists** in the official registry before adding it. Agents invent plausible package names, and attackers register them (slopsquatting).
- **Check that it's maintained and licensed** compatibly before proposing it.
- **Don't add a second library** for something an existing dependency already does.

## Auditing a project

When asked to check a project for agent sins, follow [references/audit.md](references/audit.md).

## Generalizing

This list isn't exhaustive. **If a domain has a published spec or standard (RFC, ISO, Unicode, OWASP guidance), or is security-sensitive, assume a library exists and look for it first.**
