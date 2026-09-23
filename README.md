# Undercurrent — release channel

Public, read-only distribution point for Undercurrent software. **No source
code lives here**, only built artefacts and the manifests that describe them.

## Why this repo exists

Two audiences need anonymous read access to our builds and neither needs the
source:

- **People we hand a Mac app to**, over Signal, WhatsApp or a Drive link. Their
  copy checks for updates on its own, and it has no GitHub account to
  authenticate with.
- **Deploy scripts on other machines**, which need a versioned artefact and a
  checksum they can verify without a token in the environment.

Source repositories stay private. This one is public so those two things work.

## Layout

```
<product>/appcast.json        the CURRENT release — what clients poll
<product>/history/<v>.json    every past manifest, kept for forensics
```

Artefacts themselves are attached to a GitHub release tagged
`<product>-v<version>`.

Clients read the manifest from
`https://raw.githubusercontent.com/undercurrent-ai/releases/master/<product>/appcast.json`
— a static CDN-cached file, rather than the GitHub API, which is rate-limited
to 60 anonymous requests an hour per IP and would fail for a whole office
sharing one address.

## Integrity

Every Mac app manifest carries an **Ed25519 signature** over the canonical line

```
<AppName>\n<version>\n<sha256-of-zip>\n
```

The public half of the signing key is compiled into each app; the private half
never leaves the release machine and is not in any repository. An app refuses
to install an update whose signature does not verify, so write access to this
repository is **not** enough to push code to anybody.

The version is inside the signed bytes deliberately: it stops a valid old
signature being replayed to force a downgrade.

## Publishing

Releases are cut by `~/NEXUS/scripts/release/ship.sh` — see
`~/NEXUS/scripts/release/README.md`.

## Products

| Product | What it is | Manifest |
|---|---|---|
| `breach_radar` | Breach Radar — macOS incident monitor and outreach board | [appcast.json](breach_radar/appcast.json) |
