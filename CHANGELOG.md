# Changelog

All notable changes to **REdispatch — Dispatch & Finance** will be documented in this file.

The project follows semantic versioning (MAJOR.MINOR.PATCH) for releases.

## v1.0.0 — Initial public release (2026-01-10)

### Added

- Deterministic hourly dispatch engine for Solar / Wind / BESS / Grid.
- Post-dispatch finance layer: CAPEX, OPEX, replacements, discounting, EAC, LCOE (served-energy basis).
- Golden cases (Hybrid / Solar-only / Grid-only) with expected dispatch and finance summaries.
- Test suite (unit + golden regression).
- Example cases under `examples/` (minimal, full_hybrid, renewables_only).
- Documentation: README, INPUTS.md, MODEL.md, FINANCE.md.
- License tiers and third-party notices.

## v1.1.0 — Electrical Loss Chain (2026-01-31)

### Added

- `system.json` block: `electrical_losses` with:

  - `enabled`
  - `collection_system_loss_frac`
  - `transformer_loss_frac`
  - `internal_line_loss_frac`
  - `substation_loss_frac`

- Deterministic electrical loss chain applied per timestep as fixed multipliers (no power flow), affecting only renewable power (solar+wind net) before allocation. Not applied to `grid_import_mw` or `bess_discharge_mw`.

- Defaults and validation:

  - Missing `electrical_losses` block → disabled
  - `enabled: false` overrides
  - Missing fraction fields → `0.0`
  - Fractions must be finite and in `[0, 1)`

- Hourly outputs:

  - `electrical_losses_mw` (total)
  - `loss_collection_mw`
  - `loss_transformer_mw`
  - `loss_internal_line_mw`
  - `loss_substation_mw`

- Summary outputs:

  - `electrical_losses_enabled`
  - `electrical_losses_mwh_total`
  - `loss_collection_mwh`
  - `loss_transformer_mwh`
  - `loss_internal_line_mwh`
  - `loss_substation_mwh`


### Changed

- Renewable availability definition updated:

  - `re_raw_mw = solar_net_mw + wind_net_mw` (existing per-source losses)
  - `re_total_mw` represents renewable power after the electrical loss chain when enabled
  - When disabled, `re_total_mw == re_raw_mw` (v1.0.0 parity within floating-point tolerance)

- Energy balance expanded to explicitly account for electrical losses (component-auditable).

- Golden cases extended to cover:

  - Losses disabled vs enabled parity
  - Single-component activation
  - Combined chain losses

### Fixed

- None

