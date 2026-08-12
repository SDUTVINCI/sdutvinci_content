# Vinci Content Snapshot

This repository is a human-readable, one-way snapshot exported from the Vinci
PostgreSQL content authority.

- The `main` branch is written only by the database export worker.
- Do not merge proposal branches into `main` as a publishing mechanism.
- Local changes belong on `proposal/*` branches and are not live content.
- GitHub or export failures never roll back an already published database revision.
- `.vinci/snapshot.json` and `manifest.json` describe exported revisions,
  article-credit display identities, and hashes.

The website does not read this repository at runtime.
