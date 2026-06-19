# spurin/sysbox — diveinto-labd patched fork

Tracks upstream `nestybox/sysbox` with the minimum changes needed to run the
sysbox-backed `kubernetes-pod-set` substrate on Ubuntu 26.04 / kernel 7.0.

## What differs from upstream
- `patches/0001-sysbox-fs-1003-nilguard.patch` — guards `ProcessNsMatch`
  against a nil process (nestybox/sysbox#1003). On kernel 7.0 every
  container-init sysctl write carries the `KILL_SUIDGID` flag, making the
  pre-existing nil-deref fire deterministically and crash the per-container
  FUSE server. The guard skips an optional write-cache; the write itself is
  unaffected.
- `.gitmodules` — absolute upstream URLs so this single fork builds standalone
  (no need to fork the submodule repos).
- `VERSION` — `0.7.0.1` so the patched deb is distinguishable from the
  upstream `0.7.0` deb by dpkg / the installer.
- `.github/workflows/build-deb.yml` — builds the deb via sysbox-pkgr (the same
  recipe nestybox runs by hand) and attaches it to the release for the tag.

## Release flow
Push a tag `vX.Y.Z.N` → CI builds `sysbox-ce_X.Y.Z.N.linux_amd64.deb` and
publishes it on that release. The diveinto-labd agent's sysbox role downloads
that asset (no per-host source build).
