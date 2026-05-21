# Parametric debt — Coaster-Chainmail

Captured: 2026-05-20 (session that scaffolded the project + relocated VarSets).

## Current audit state

`python3 scripts/audit_parametric.py` reports **7 unbound literal dimensions**, all in `Coaster_Chainmail.FCStd`. `FabricArray.FCStd` and `Params.FCStd` are clean.

These are the values that should be bound to `Params.VarSet` before any future geometry edit. Until then, a tweak to e.g. tile thickness via Params won't propagate — the Pad still uses a literal `0.6`.

## Findings

| # | Object | Property | Current value | Proposed Params var | Notes |
|---|--------|----------|---------------|---------------------|-------|
| 1 | `Sketch001.Constraints[19]` | DistanceY | 7 mm | _TBD — open sketch and pick a semantic name_ | Last constraint in Sketch001; unnamed |
| 2 | `Pad001.Length` | Length | 0.6 mm | **`TileThickness`** (exists) | Already in Params — just bind |
| 3 | `Pad001.Length2` | Length2 | 10.0 mm | _new var, e.g._ `PadLength2` or `BaseExtrusionDepth` | Two-direction pad; second extent |
| 4 | `Chamfer.Size` | Size | 0.2 mm | _new var, e.g._ `EdgeChamfer` | Edge-break chamfer |
| 5 | `Fillet.Radius` | Radius | 0.2 mm | _new var, e.g._ `Fillet1Radius` | First fillet pass |
| 6 | `Fillet001.Radius` | Radius | 0.4 mm | _new var, e.g._ `Fillet2Radius` | Second fillet pass |
| 7 | `Fillet002.Radius` | Radius | 0.5 mm | _new var, e.g._ `Fillet3Radius` | Third fillet pass |

Audit task tracking these is `#5 — Bind 12 audit-flagged literal dimensions to Params`.

## Exemptions

### B-spline Weight constraints (5)

Sketches `Sketch002`, `Sketch005`, `Sketch007`, `Sketch008`, `Sketch009` each carry a `Weight` constraint that is a literal. Weight constraints drive **B-spline pole weights** (curve shaping for the chainmail link's profile). They are cosmetic / shape-tuning, not load-bearing dimensional values.

Decision (2026-05-20): leave as literals. The audit script's `DIMENSIONAL_TYPES` table is modified to omit Weight (Type 18) so it no longer flags these. If a future redesign wants to tune the curve shape parametrically, re-add Type 18 to the table and add `Curve1Weight`…`CurveNWeight` to Params.

## Other notes from this session

### Cross-doc Link broke on FCStd rename

When this project was renamed from `Coaster-chain/Coaster_Chanmail.FCStd` to `Coaster-Chainmail/Coaster_Chainmail.FCStd`, the `App::Link` in `FabricArray.FCStd` (Label `Single001`) lost its target because its `XLink file="..."` attribute still pointed at the old filename. Detected by `LinkedObject = null` and "Invalid" state on reopen. Fixed by setting `link.LinkedObject = Coaster_Chainmail.Body001` and re-saving.

Future rule: rename FCStd files **inside FreeCAD via Save-As-and-delete-old**, not via `mv` on the filesystem with FreeCAD running. Saved as a global feedback memory so other projects don't repeat this.

### VarSet migration

Both source documents originally had their own in-document VarSet (`Coaster_Chanmail`: 8 link-geometry vars; `FabricArray`: 3 array-layout vars). Migrated into a single `Params.FCStd` with one VarSet (11 properties total) by `macros/migrate_varset_to_params.FCMacro`. All `<<VarSet>>.X` expressions rewritten to `<<Params>>#VarSet.X` (the canonical cross-doc form per the global rule).
