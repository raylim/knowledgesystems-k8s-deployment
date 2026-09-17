# Beta WSI reconciliation ledger

As of 2026-09-17, the live beta WSI deployment was inspected in Kubernetes
context `cbioportal-msk-666628074417` before selecting source revisions.

| Component | Reconciled revision | Promotion state | Notes |
| --- | --- | --- | --- |
| Frontend | `b913f322b` (`codex/reconcile-beta-frontend`) | Build and publish required | Includes server-side clinical table pagination/sorting and cancer-root filtering. Based on the verified beta lineage `18c02c853`. |
| Backend | `c5a6af962f` (`codex/reconcile-beta-backend-validated`) | Build and publish required | Includes deterministic clinical pagination, ordered sample response keys, sample-bounded molecular reads, and Spotless formatting. |
| Core importer | `52d8b1789` (`codex/reconcile-beta-core`) | Source revision ready | Includes native WSI loading, de-identification validation, importer error propagation, and derived-table verification before hydration is recorded. |
| Compose hydration/release gate | `0fdbd5a` (`codex/reconcile-beta-compose`) | Source revision ready | Includes release-gated, study-scoped WSI and molecular hydration with fail-closed completeness checks. |
| PDM serving pipeline | `291c0017` (`codex/reconcile-beta-pdm-current`) | Source revision ready | Includes fingerprint-bound WSI serving manifest and contract tests. |
| Tile server | `e876b5f` (`codex/reconcile-beta-tile`) | Keep current immutable release | Existing beta tile image is already pinned by digest and passes the serving-contract test suite. |
| Kubernetes deployment | `1889cade` (`codex/reconcile-beta-k8s`) | Update only after image publication | This is the live beta release lineage and preserves immutable frontend, backend, and tile identities. |

## Live identities recorded before reconciliation

- Frontend preview host: `6aaad47e4dd99b00083d160d--cbioportalfrontend.netlify.app`
- Frontend revision: `ca327132101fcce80732b53b098083a9e273344b`
- Backend revision: `e56f407b2d31b45a6b1a314c5152be61b4954ae0`
- Backend image digest: `sha256:780fa5ec83b26eb5a0634a45a3b0494cc0144645a0683c4b69b210e7e668e9bd`
- Tile image digest: `sha256:0638d904f004bc0cd2f678869ccc7dab8ec4e092c0b30f9872cb1095b4942197`

The backend and frontend revisions above are new source candidates and do not
have deployment image digests yet. No Kubernetes image field is changed until
CI publishes and records those immutable digests.

## Explicitly not promoted

- The dirty frontend WSI/timeline refactor was not copied. The verified beta
  lineage already contains the intended clinical-event timeline contract and
  timepoint dependency behavior; the dirty version removed those protections.
- Local Compose study snapshots, generated `__pycache__` files, and the dirty
  triage deployment edits were not promoted. They are source-local artifacts or
  unrelated deployment regressions, not release identities.
- No live database hydration was run from an unverified or partial bundle. The
  mskimpact and coad hydration path must use the Compose release gate and its
  study-scoped manifest before mutating ClickHouse.
