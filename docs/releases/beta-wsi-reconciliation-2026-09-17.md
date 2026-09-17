# Beta WSI reconciliation ledger

As of 2026-09-17, the live beta WSI deployment was inspected in Kubernetes
context `cbioportal-msk-666628074417` before selecting source revisions.

| Component | Reconciled revision | Promotion state | Notes |
| --- | --- | --- | --- |
| Frontend | `d5bd7a209f063739f7aca773b0c4f0d69d9894c2` (`codex/reconcile-beta-frontend`) | Build and publish required | Includes server-side clinical table pagination/sorting, cancer-root filtering, same-origin API routing, an HTTP-compatible dev server default, and the corrected dated/undated pathology-slide timeline behavior. |
| Backend | `c5a6af962f36b75a7a8a1cb1dbd995cc3286f70` (`codex/reconcile-beta-backend-validated`) | Build and publish required | Includes deterministic clinical pagination, ordered sample response keys, sample-bounded molecular reads, and Spotless formatting. |
| Core importer | `d28e25e3736dd5d447c7e246e4433f9c214a1a3d` (`codex/reconcile-beta-core`) | Source revision ready | Includes native WSI loading, de-identification validation, ClickHouse 3.2 schema alignment, importer error propagation, and derived-table verification before hydration is recorded. |
| Compose hydration/release gate | `44f23826461ffabe5871acb5d5ad49488c5c8176` (`codex/reconcile-beta-compose`) | Source revision ready | Includes release-gated, study-scoped WSI and molecular hydration with fail-closed completeness checks, exact timeline image membership, and a pinned tile-server checkout. |
| PDM serving pipeline | `ded6990` (`codex/reconcile-beta-pdm-current`) | Source revision ready | Includes the serving-pipeline lineage and v3 timeline contract requiring sorted, explicit `IMAGE_IDS`. |
| Tile server | `e876b5fad065cf36622af596eee2b0d8356d3c1c` (`codex/reconcile-beta-tile`) | Keep current immutable release | Existing beta tile image is already pinned by digest and passes the serving-contract test suite. |
| Kubernetes deployment | `02e40b6b` (`codex/reconcile-beta-k8s`) | Update only after image publication | Reconciles current upstream manifests and restores the immutable beta WSI release validator gate. |

## Live identities recorded before reconciliation

- Frontend preview host: `6aaad47e4dd99b00083d160d--cbioportalfrontend.netlify.app`
- Frontend revision: `ca327132101fcce80732b53b098083a9e273344b`
- Backend revision: `e56f407b2d31b45a6b1a314c5152be61b4954ae0`
- Backend image digest: `sha256:780fa5ec83b26eb5a0634a45a3b0494cc0144645a0683c4b69b210e7e668e9bd`
- Tile image digest: `sha256:0638d904f004bc0cd2f678869ccc7dab8ec4e092c0b30f9872cb1095b4942197`

The backend and frontend revisions above are source candidates and do not have
deployment image digests yet. No Kubernetes image field is changed until CI
publishes and records those immutable digests.

## Explicitly not promoted

- The frontend WSI/timeline changes are now captured in the verified frontend
  revision above; they preserve the clinical-event timeline contract while
  adding the explicit undated-slide fallback.
- Local Compose study snapshots, generated caches, and dirty triage deployment
  edits were not promoted. They are source-local artifacts or unrelated
  deployment regressions, not release identities.
- No live beta database hydration was run. Dev hydration was completed and
  verified for both `mskimpact` and `coad_msk_2025`; beta still requires
  immutable CI-published images and the release-gate manifest before mutation.
