# nile-catalog

Public catalog of Nile runtime packages. The Nile dashboard reads `packages.json` to show which `nile-*-runtime`
packages exist, their versions and their digests. A version is offered only if it is listed here, pinned by digest.
Pushing an image to the registry alone adds nothing.

This repository holds no code and no images.

## packages.json

See `schema/packages.schema.json`. Versions are listed newest first, and `latest` must be one of them. The full image
reference is `<registry>/<path>@<digest>`, for example `ghcr.io/perkbridge/nile-mt5-runtime@sha256:...`.

## Adding a version

Open a pull request that adds the version to the package's `versions` (newest first), updates `latest` if needed, and
updates `updated_at`. A person reviews and merges it. `packages.json.sig` is reserved for a detached signature with the
release key (not used yet).
