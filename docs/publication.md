# Publication requirements

Beta and stable use separate app identities, commands, update feeds and release metadata. Stable is
a later channel; no stable download is offered yet.

Each release requires a verified signed/notarized DMG containing the app and its bundled CLI,
SHA-256 checksums, public release notes and supported architecture/macOS metadata. Updater artifacts
come from the same final signed and stapled app as the DMG and carry a detached updater signature.

## Beta releases and feed

Each Beta build `0.3.N` is one release tagged `beta-v0.3.N` with exactly these assets:

| Asset | Content |
|---|---|
| `FastPaste_Beta_0.3.N_aarch64.dmg` | Signed, notarized and stapled installer |
| `FastPaste_Beta_0.3.N_aarch64.app.tar.gz` | Updater archive of the same stapled app, `FastPaste Beta.app` at its root |
| `FastPaste_Beta_0.3.N_aarch64.app.tar.gz.sig` | Detached updater signature, bound to the archive name and version |
| `SHA256SUMS` | SHA-256 of the DMG, archive and signature |
| `beta.json` | Update manifest for this version |

The release body states the version, architecture, minimum macOS, the source commit and the SHA-256
of every asset.

The Beta feed is
`https://github.com/SlvrPeak/FastPaste-releases/releases/latest/download/beta.json`: the
`beta.json` asset of the release GitHub marks as latest. Its format:

```json
{
  "version": "0.3.N",
  "notes": "FastPaste Beta 0.3.N",
  "pub_date": "<RFC 3339 signing time>",
  "platforms": {
    "darwin-aarch64": {
      "signature": "<contents of the .sig asset>",
      "url": "https://github.com/SlvrPeak/FastPaste-releases/releases/download/beta-v0.3.N/FastPaste_Beta_0.3.N_aarch64.app.tar.gz"
    }
  }
}
```

A release moves through three states, and only the last changes the feed:

1. **Draft.** The publisher creates the draft, uploads every asset and checks the stored digests.
   An interrupted draft was never public and may be deleted and recreated.
2. **Prerelease.** Publishing makes the tag and assets public and immutable (immutable releases
   are enabled for this repository). A prerelease is never selected as latest, so the feed is
   unchanged. Every asset is then downloaded without credentials and its SHA-256 compared with the
   verified hashes.
3. **Promoted.** The release is changed to a full release marked latest, and the feed is read back
   without credentials and compared with the uploaded `beta.json`. Promotion is held until the
   owner approves the first artifact hashes; after that, every verified Beta from `main` is
   promoted without a manual step.

Only the newest Beta publishes: a build older than one already published is skipped, a published
version is never replaced with different bytes, and each new release's source must descend from
the previous one's. Prereleases left by a skipped or held promotion stay downloadable.

The Homebrew cask is updated only from a promoted release, using its verified public DMG URL and
SHA-256.

Because the Beta feed is `releases/latest`, only `beta-v` releases may be published here while Beta
clients poll it. A stable channel, or any other release in this repository, first requires moving
the Beta feed to its own endpoint.

## Content

Public payloads contain binaries and distribution metadata. Build logs, session recordings,
private documentation, credentials, private-key files and private build receipts remain outside
this repository.

An in-app updater asks before installation/restart. Homebrew and in-app updates may coexist only
after tests establish ownership, locking, version tracking, command links and recovery behavior.
