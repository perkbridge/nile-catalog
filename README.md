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
updates `updated_at`. A person reviews and merges it. The `version` label is free text such as `1.4.0` or `main-9f53c06`.

`transfer_bytes` (optional) is the download size the dashboard shows: the compressed bytes of the image, the sum of the
layer sizes in its manifest (not the config blob, and not the disk used after the pull, which is several times
larger). Read it from the registry for the exact digest, for example:

```
docker buildx imagetools inspect --raw ghcr.io/perkbridge/nile-mt5-runtime@sha256:... | jq '[.layers[].size] | add'
```

A version without `transfer_bytes` shows no size. The value is covered by the signature like everything else.

## Signature

`packages.json.sig` is an Ed25519 signature over the exact bytes of `packages.json`, made with the catalog key
(`nile-catalog-1`). The Control Plane and the agents refuse a catalog without a valid signature (the Control Plane can be told to allow
unsigned catalogs for development only). Every change to `packages.json` needs a new signature, made by the key holder with the
Nile Control Plane tool:

```
control-plane release catalog-sign   --file packages.json --key <private key file> --key-id nile-catalog-1
control-plane release catalog-verify --file packages.json
```

## limitations.json

What each version of a package was tested with and what it cannot do. The dashboard shows it under Help, Current
limitations, per connector and per version. See `schema/limitations.schema.json`. Each version lists `tested_with`
(operating system, connector with its version, status, optional note of at most 300 characters) and `limitations`
(status, item, description of at most 600 characters). A status is `tested` (it was run and worked on a real host),
`unverified` (not run, or not on a real host) or `unsupported` (it does not work or does not exist yet). Write only what
was actually observed.

The file is data only: it changes what the dashboard says, never what may be pulled or run. It is signed like
`packages.json` and needs a new signature after every change:

```
control-plane release catalog-sign   --file limitations.json --key <private key file> --key-id nile-catalog-1
control-plane release catalog-verify --file limitations.json
```
