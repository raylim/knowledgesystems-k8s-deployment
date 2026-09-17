# Beta WSI reconciliation ledger

As of 2026-09-17, the live beta WSI deployment was inspected in Kubernetes
context `cbioportal-msk-666628074417` before selecting source revisions.

| Component | Reconciled revision | Promotion state | Notes |
| --- | --- | --- | --- |
| Frontend | `d5bd7a209f063739f7aca773b0c4f0d69d9894c2` (`codex/reconcile-beta-frontend`) | Build and publish required | Includes server-side clinical table pagination/sorting, cancer-root filtering, same-origin API routing, an HTTP-compatible dev server default, and the corrected dated/undated pathology-slide timeline behavior. |
| Backend | `c5a6af962f36b75ac7a8a1cb1dbd995cc3286f70` (`codex/reconcile-beta-backend-validated`) | Build and publish required | Includes deterministic clinical pagination, ordered sample response keys, sample-bounded molecular reads, and Spotless formatting. |
| Core importer | `5fa20874ccd3b914f6bf128b18cdc07436ded57d` (`codex/reconcile-beta-core`) | Source revision ready | Includes native WSI loading, de-identification validation, ClickHouse 3.2 schema alignment, importer error propagation, active-part staging verification, and derived-table verification before hydration is recorded. |
| Compose hydration/release gate | `30be3b7103076e6ebf773e1c5342f0f502cf0b7e` (`codex/reconcile-beta-compose`) | Source revision ready | Includes explicit canonical IMPACT inventory generation, study-scope enforcement, pinned derived-table/timeline dependencies, and fail-closed WSI/molecular release checks. |
| PDM serving pipeline | `ded699007c758457ac3b3dca5c4dca48417e029a` (`codex/reconcile-beta-pdm-current`) | Source revision ready | Includes the serving-pipeline lineage and v3 timeline contract requiring sorted, explicit `IMAGE_IDS`. |
| Tile server | `e876b5fad065cf36622af596eee2b0d8356d3c1c` (`codex/reconcile-beta-tile`) | Keep current immutable release | Existing beta tile image is already pinned by digest and passes the serving-contract test suite. |
| Kubernetes deployment | `5fd45dae2cbf3e6cd0f599df914f6e964f77371d` (`codex/reconcile-beta-k8s`) | Update only after image publication | Reconciles current upstream manifests and restores the immutable beta WSI release validator gate; promotion uses an in-place beta maintenance window. |

## Live identities recorded before reconciliation

- Frontend preview host: `6aaad47e4dd99b00083d160d--cbioportalfrontend.netlify.app`
- Frontend revision: `ca327132101fcce80732b53b098083a9e273344b`
- Backend revision: `e56f407b2d31b45a6b1a314c5152be61b4954ae0`
- Backend image digest: `sha256:780fa5ec83b26eb5a0634a45a3b0494cc0144645a0683c4b69b210e7e668e9bd`
- Tile image digest: `sha256:0638d904f004bc0cd2f678869ccc7dab8ec4e092c0b30f9872cb1095b4942197`

The backend and frontend revisions above are source candidates and do not have
deployment image digests yet. No Kubernetes image field is changed until CI
publishes and records those immutable digests. Beta remains on its existing
route during preparation; the portal may be taken offline for the final
hydration and restart.

## Explicitly not promoted

- The frontend WSI/timeline changes are now captured in the verified frontend
  revision above; they preserve the clinical-event timeline contract while
  adding the explicit undated-slide fallback.
- Local Compose study snapshots, generated caches, and dirty triage deployment
  edits were not promoted. They are source-local artifacts or unrelated
  deployment regressions, not release identities.
- No live beta database hydration was run. Dev hydration was completed and
  verified for representative studies only; beta still requires the complete
  canonical IMPACT inventory, immutable CI-published images, and the
  source-aware release-gate manifest before mutation.
- The dev stack is frozen for the live demo. Release preparation and beta
  maintenance must not restart or rehydrate the dev containers, database, or
  caches.
