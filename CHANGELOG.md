# Changelog

All notable changes to this project are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and
this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

`Unreleased` holds work that is merged but not yet published. Move entries into
a dated version section when a release goes out — an `Unreleased` block that
survives three releases is a changelog nobody is maintaining.


## [Unreleased]

## [1.3.1] — 2026-08-11

### Fixed

- **The API reference documented threat-type values that do not exist.** It
  listed `injection`, `jailbreak`, `pii` and `exfiltration`; the API returns
  `prompt_injection`, `pii_leak` and `data_exfiltration`. Anyone who followed
  this reference and matched on those strings was matching on nothing, and the
  branch never fired. The values are now the API's own, with a note that they
  are `snake_case` and several read differently from the plain-English name of
  the attack.


No user-facing changes yet since this changelog was introduced.

