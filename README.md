# Enterprise Solutions Definitions

This repository contains the declarative definitions of enterprise solutions supported by Amiasea.

Each solution is an independently identifiable entry that may comprise multiple artifacts, including Pomology documents. Solution entries are maintained together in this repository and published as a versioned collection.

## Repository Structure

```text
.
├── .github/
│   └── workflows/
│       └── release.yml
├── pomology.cue
├── README.md
└── <solution>/
    ├── pomology.json
    └── <other solution artifacts>
```

- **`<solution>/`** — Contains the artifacts belonging to an individual solution. Each solution has its own directory, identified by its solution codename.
- **`pomology.cue`** — Shared CUE schema for validating Pomology documents.
- **`.github/workflows/release.yml`** — Validates the applicable documents and publishes the solution definitions when a version tag is pushed.

The repository may contain any number of solution entries. A Pomology is one type of solution artifact, not the definition of the entire solution or a requirement that every solution contain only a Pomology.

## Validation

[CUE](https://cuelang.org/) is used to validate Pomology documents against the shared schema.

The schema enforces structural and data constraints, including:

- Unique, data-defined variety and aspect-option codenames.
- Valid repository identifiers.
- Exactly one root Cordon per Pomology.
- Valid tier references.
- An options mapping for every declared variety, with values restricted to the declared aspect options.

To validate a Pomology document locally, run the following command from the repository root:

```shell
cue vet -c pomology.cue <solution>/pomology.json
```

The release workflow discovers and validates every `pomology.json` located directly within a solution directory. Additional artifact types may have their own validation requirements.

## Releases

Releases are triggered by pushing a Git tag beginning with `v`, such as `v1.0.0`.

```shell
git tag v1.0.0
git push origin v1.0.0
```

For each release, the workflow:

1. Validates the discovered Pomology documents.
2. Packages the shared schema and solution artifacts into a ZIP archive.
3. Generates a GitHub build-provenance attestation for the archive.
4. Publishes the archive as a GitHub Release asset.

The release asset follows this naming convention:

```text
enterprise_solutions-<version>.zip
```

The archive preserves the solution directory structure and groups each solution's artifacts under its corresponding directory. The release represents the collection of solution definitions at the tagged revision.

## Provenance and Integrity

The workflow generates a GitHub build-provenance attestation for the release archive, providing verifiable information about the artifact's build provenance.

An attestation does not, by itself, make a GitHub Release immutable. Release immutability must be configured separately where supported.

## Scope

This repository owns the declarative artifacts that define enterprise solutions. It is not limited to Pomology documents; additional artifacts may be maintained alongside them as required by each solution.

The Terraform actualization process and the resulting GitHub repository structures belong to their respective Amiasea components.