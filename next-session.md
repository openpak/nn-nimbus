# Next session — nn-nimbus

Updated 2026-09-24.

3DS client (Luma): account manager (`nimbus.cia`/`.3dsx`), plugin
(`nimbus.3gx`) and IPS patches — Pretendo's nimbus pointed at OpenPak.
Built and released on `openpak-v1` (2026-09-12); per the 3DS PRD, one
hardware sign-in from being measurable.

Current status 2026-09-24: latest OpenPak tag still `openpak-v1` (c94dca6;
`v2.1.1` is upstream's). Since then only CI (release now triggers on
`v*.*.*` tags, not `openpak-v*`) and docs commits.

## Where things stand

- Last release `openpak-v1` (2026-09-12); no code since, only CI + docs.
  The next release needs a `v*.*.*` tag (ef8dcd6) — it must not collide with
  upstream's `v1.x`/`v2.x` tags already in the repo.
- One-command build: `docker/build.sh` (toolchain image builds CTRPF
  serially — its Makefile races under `-j`; then plugin, app, and the IPS
  patches when the six dumps are in place).
- The build surfaced the rebrand-era bug: the socket patch resolved
  `*.openpak.org` to `openpak_server_ip`, a symbol defined nowhere. Now
  defined, and matching what `nn-sssl-dns` serves: nncs1 + every
  `*.openpak.org` name → 145.241.199.19, nncs2 → 145.241.228.207.
- Server side verified reachable for a sign-in: `account.openpak.org`
  answers `healthz` through Cloudflare; `nasc.openpak.org` has no public
  DNS by design — the socket patch resolves it on-console.
- The six IPS patches assemble against synthetic images; shipping them
  needs sysmodule dumps only a real console can provide
  (`patches/<module>/code.bin`: act, friends, http, socket, ssl, miiverse;
  dumps gitignored).
- 2026-09-15 docs pass committed: `CHANGELOG.md`, `docs/`, `prds/` stubs.

## Next steps

1. DS3-1 — the one sign-in: install `nimbus.cia` with FBI, run, pick the
   **Pretendo** button (that is the OpenPak path), sign in; watch
   `nn-account` logs. Converts five PRD rows from believed to known.
2. Dump the six modules per `DECOMPRESSING.md`, re-run `docker/build.sh`,
   copy the IPS patches to `3ds/nimbus/update/` on the SD card.
3. Luma prereqs: "Enable loading external FIRMs and modules" + "Enable
   game patching" (13.0+); plugin via Rosalina → Plugin Loader → Enabled.

## Pointers

- README: OpenPak usage steps 1–6, build, DECOMPRESSING.md
- ../prds/platform-3ds-prd.md — DS3-0 done, DS3-1 next, §2 the build story

## Scratch (research and throwaway work)

Decompiles, Ghidra projects, dumps, exefs/romfs extracts, packet captures,
strace and emulator logs, probe harnesses: put them in
`~/REPOS/Openpak/scratch/<topic>`. That folder is a local mount of the media pool,
outside every repository, so nothing in it is committed. Never use `/tmp` (a
shared 15 GB RAM disk) or elsewhere on `/home` for this. Keys and signing
material never go there. Rule: `docs/playbooks/conventions.md` in the workspace.
