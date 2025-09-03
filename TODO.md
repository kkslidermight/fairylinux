# TODO — Atomic AlmaLinux Respin

## 1. Initial setup

- [x] **Configure registry settings** in `.github/actions/config/action.yml`:
  - [x] `REGISTRY` (default: `ghcr.io`)
  - [x] `REGISTRY_USER`
  - [x] `IMAGE_PATH`
  - [x] `IMAGE_NAME`
  - [x] `PLATFORMS` (e.g., `"amd64,arm64"`)
  - [x] If not using GitHub registry, replace `REGISTRY_TOKEN: ${{ secrets.GITHUB_TOKEN }}` with the correct secret.
- [x] **Select base desktop image** in `Dockerfile`:
  - [ ] Default: `quay.io/almalinuxorg/atomic-desktop-gnome:10`
  - [x] Optional: `quay.io/almalinuxorg/atomic-desktop-kde:10`
  - [ ] If using a different signing key, update `upstream-public-key` in `.github/workflows/build.yml` or disable verification.

## 2. Container signing (optional, recommended)

- [x] Generate a Cosign key pair (local):

```sh
podman run --rm -it -v /tmp:/cosign-keys bitnami/cosign generate-key-pair
```

- [x] Add `cosign.pub` to the repository root (public).
- [x] Store `cosign.key` securely; never commit it.
- [x] Add a GitHub Actions secret named `SIGNING_SECRET` containing the contents of `cosign.key`.

## 3. Cloudflare R2 for ISO storage (optional)

- [x] Sign up for Cloudflare R2 and create a bucket.
- [x] Create an API token with Object Read & Write permissions limited to the bucket.
- [x] Add the following GitHub secrets:
  - [x] `R2_ACCOUNT_ID`
  - [x] `ACCESS_KEY_ID`
  - [x] `SECRET_ACCESS_KEY`
  - [x] `BUCKET`
- [x] In `.github/workflows/build-iso.yml`:
  - [x] Uncomment the secret definitions.
  - [x] Set `upload-to-cloudflare: true` in the workflow inputs.
- [ ] (Optional) Enable the bucket's Public Development URL for downloads.

## 4. Customize the respin

- [ ] Add files to `files/system/` (preserve structure and permissions).
- [ ] Edit `files/scripts/10-base.sh` to start customizations.
- [ ] Add additional scripts using the `XX-name.sh` numeric prefix pattern.
- [ ] Avoid modifying `build.sh`, `cleanup.sh`, `90-signing.sh`, or `91-image-info.sh` unless necessary.
