# docker-kavita

Docker image builder for the GDS-enabled Kavita builds from [`arkx82/Kavita`](https://github.com/arkx82/Kavita).

The image workflow downloads Kavita release artifacts such as:

```text
https://github.com/arkx82/Kavita/releases/download/{version}/kavita-linux-{arch}.tar.gz
```

`{arch}` is resolved to `x64` for `linux/amd64` and `arm64` for `linux/arm64`.

## Build and publish

Run **Actions → buildx → Run workflow** in [`arkx82/docker-kavita`](https://github.com/arkx82/docker-kavita), then choose:

- `kavita_repo`: `arkx82/Kavita`
- `kavita_version`: a release tag, or `latest`
- `kavita_app_version`: optional Kavita app version tag, for example `0.9.0.5`
- `platforms`: `linux/amd64,linux/arm64` by default

The workflow publishes:

```text
ghcr.io/arkx82/kavita:latest
ghcr.io/arkx82/kavita:gds
ghcr.io/arkx82/kavita:{version}
ghcr.io/arkx82/kavita:{version}-gds
ghcr.io/arkx82/kavita:{kavita_app_version}
ghcr.io/arkx82/kavita:{kavita_app_version}-gds
```

`latest` always points to the newest successful Docker build. When this workflow is triggered from `arkx82/Kavita`, the GDS asset workflow passes the Kavita app version from `Kavita.Common/Kavita.Common.csproj` automatically.

If your Kavita release assets use another URL shape, pass `kavita_download_url_template` manually. It supports `{version}` and `{arch}` placeholders.

For a GitHub release asset, the expected asset names are:

```text
kavita-linux-x64.tar.gz
kavita-linux-arm64.tar.gz
```

For the default multi-arch build, the Kavita release must include both `kavita-linux-x64.tar.gz` and `kavita-linux-arm64.tar.gz`.
If only `kavita-linux-x64.tar.gz` exists, run the workflow with `platforms` set to `linux/amd64`.
The archive may contain either `config/appsettings-init.json` or the older `config/appsettings.json`; both are supported.

The Dockerfile still uses the compatible base image from `ghcr.io/by275/base` by default. If you publish your own base image later, override `BASE_IMAGE` and `PREBUILT_IMAGE` in the workflow build args.
