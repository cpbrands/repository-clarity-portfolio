# Finding a Broken Install Path in a Buildable TypeScript Repository

## A Read-Only Repository Clarity Audit of NanoClaw Dashboard

**Project:** [NanoClaw Dashboard](https://github.com/nanocoai/nanoclaw-dashboard)
**Audit date:** September 3, 2026
**Authoritative branch:** `main`
**Baseline:** [`c0db708b890bc8e07498d65fe29061402ea460a0`](https://github.com/nanocoai/nanoclaw-dashboard/commit/c0db708b890bc8e07498d65fe29061402ea460a0)
**Audit type:** Independent read-only portfolio exercise; not commissioned or endorsed by the maintainer

## Summary

NanoClaw Dashboard is a small TypeScript package that receives monitoring snapshots and serves a web dashboard. Its repository installed cleanly, compiled successfully, and produced a valid package dry-run.

Yet its primary installation instruction was broken.

The repository had moved to the `nanocoai` organization and its package manifest identified the package as `@nanoco/nanoclaw-dashboard` version `0.3.0`. The README still told users to install and import `@qwibitai/nanoclaw-dashboard`.

A public-registry check resolved the uncertainty:

* `@nanoco/nanoclaw-dashboard` existed at version `0.3.0` and was tagged `latest`.
* `@qwibitai/nanoclaw-dashboard` returned `404 Not Found`.

The code could build while the documented path into the code failed. This is precisely the kind of gap a repository clarity audit is designed to expose.

## The Client-Style Question

The simulated client question was:

> Can a new owner or contributor rely on this repository's authoritative documentation and metadata to understand, install, and validate the project?

The audit did not attempt to certify runtime security, deploy the application, or redesign its architecture. It examined one bounded question using repository and registry evidence.

## Establishing Authority

The audit first froze the target:

* Repository: `nanocoai/nanoclaw-dashboard`
* Branch: `main`
* Commit: `c0db708b890bc8e07498d65fe29061402ea460a0`

At inspection time, GitHub exposed only `main`, with no open pull requests or open issues returned. That made the repository's visible authoritative state unambiguous. Recommendations in the audit remained external proposals with no upstream authority.

## What Was Inspected

The repository contained 19 tracked files, including:

* README and package metadata.
* TypeScript configuration.
* CLI, HTTP server, routing, data-store, and type modules.
* Seven dashboard-page modules.

The audit compared:

* README instructions against `package.json`.
* `package.json` against `package-lock.json`.
* Documented API routes against implemented routes.
* Declared quality checks against the tracked repository.

## Validation Results

| Check                                           | Result              | Meaning                                                                       |
| ----------------------------------------------- | ------------------- | ----------------------------------------------------------------------------- |
| `npm ci`                                        | Passed              | The committed dependency lock installed successfully in the audit environment |
| `npm run build`                                 | Passed              | TypeScript compiled successfully                                              |
| `npm pack --dry-run`                            | Passed              | The compiled package could be assembled as `@nanoco/nanoclaw-dashboard@0.3.0` |
| Public registry: `@nanoco/nanoclaw-dashboard`   | Found               | Version `0.3.0` was published as `latest`                                     |
| Public registry: `@qwibitai/nanoclaw-dashboard` | `404 Not Found`     | The README's installation target was unavailable publicly at audit time       |
| Automated tests                                 | None declared       | Compilation was the only repository-declared automated check observed         |
| GitHub Actions                                  | No workflow present | No tracked GitHub Actions gate was observed                                   |

These results demonstrate an important distinction: a passing build proves that TypeScript compiled. It does not prove that the instructions presented to a new user are correct.

## Findings

### 1. The documented install path was broken

The README used the old `@qwibitai` package scope in its title, installation command, and import example. The current manifest and public registry used `@nanoco`.

**Consequence:** a user following the authoritative README installation command received a public-registry `404`.

### 2. Repository ownership metadata was stale

GitHub served the project under `nanocoai`, while the README and `package.json` still referenced `qwibitai` URLs.

**Consequence:** readers and package tooling were directed toward an earlier ownership identity or a redirect rather than the current canonical location.

### 3. The lockfile described an older root package

`package-lock.json` identified the root as `@qwibitai/nanoclaw-dashboard` version `0.1.0`. `package.json` identified it as `@nanoco/nanoclaw-dashboard` version `0.3.0`.

**Consequence:** the committed reproducibility record and current manifest disagreed about the package being built, even though `npm ci` succeeded.

### 4. The API table was incomplete

The implementation included message, log-streaming, log-push, and dynamic agent-group routes that were absent from the README API table.

**Consequence:** the primary documentation did not expose the complete implemented route surface. Maintainer confirmation would still be required before labeling every implemented route as public API.

### 5. No behavioral test or tracked CI gate was visible

The repository declared a build command but no test command, test files, or GitHub Actions workflow.

This was recorded as an optional quality improvement, not a proven software defect. External CI or private tests may exist outside the repository.

## Recommended Bounded Correction

After maintainer confirmation, one focused documentation-and-metadata change could reconcile:

1. `README.md` — current package name, organization links, and intended public API routes.
2. `package.json` — canonical repository URL.
3. `package-lock.json` — current root name and version through the normal package-manager workflow.

Acceptance would require clean installation, compilation, package dry-run, matching package identities, and no implementation changes.

No upstream issue or pull request was created as part of this exercise.

## What I Did

* Established an exact unfamiliar-repository baseline.
* Mapped the complete small repository.
* Distinguished authoritative state from external recommendations.
* Compared documentation, manifests, lockfile, implementation, and registry state.
* Ran safe installation, build, and packaging checks.
* Separated verified facts, inferences, and unresolved questions.
* Produced a prioritized remediation plan without touching production or upstream code.

## Limitation Discovered in My Process

Exact installation and build times were captured, but I did not start the total audit timer before baseline collection. I recorded total duration as “not captured” rather than estimating it.

The audit template will therefore require the timer to start before the first repository inspection.

## What This Demonstrates

This case study demonstrates evidence-based repository auditing and documentation reconciliation. It does not claim senior full-stack engineering, production deployment, security certification, penetration testing, database migration, or operation of a live customer system.

The central lesson is simple:

> A repository can build successfully while its authoritative instructions still prevent a new user from getting through the front door.
