# Publication requirements

Beta and stable use separate app identities, commands, update feeds and release metadata. Beta
releases are marked prerelease. Stable is a later channel; no stable download is offered yet.

Each release requires a verified signed/notarized DMG containing the app and its bundled CLI,
SHA-256 checksums, public release notes and supported architecture/macOS metadata. Updater artifacts
come from the same final signed and stapled app as the DMG and carry a detached updater signature.

Approve the exact artifact hashes before publication. Download the published bytes independently
and verify them before updating a feed pointer or Homebrew cask. Published version assets are
immutable; a retry cannot replace them with different bytes.

The proposed beta feed is `channels/beta.json`, served over HTTPS from this repository. It is created
only after an approved updater archive is available; placeholder URLs/signatures are never a feed.
Keep beta independent of GitHub's latest stable release selection.

Public payloads contain binaries and distribution metadata. Build logs, session recordings,
private documentation, credentials and private-key files remain outside this repository.

An in-app updater asks before installation/restart. Homebrew and in-app updates may coexist only
after tests establish ownership, locking, version tracking, command links and recovery behavior.
