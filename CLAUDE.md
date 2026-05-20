# Project rules — Coaster-Chainmail

A 3D-printable chainmail-fabric coaster. Two FreeCAD documents:

- `Coaster_Chainmail.FCStd` — the source unit (the chainmail link / cell geometry)
- `FabricArray.FCStd` — the printable sheet (array of the unit, this is what slices to `3mf/ChainMailSheet.3mf` and `stl/FabricArray.stl`)

A successful test print has been produced; this project is in **print-deliverable** state.

## Project conventions

Follow the global rules in `~/.claude/CLAUDE.md` (everything parametric, no raw coordinate edits, datum-plane attachment, decoupled clearance Params, macros under `macros/`).

### Directory layout (print-deliverable convention)

- `3mf/` — slicer-ready print files. **Tracked.** Published to Creality.
- `stl/` — printable STL meshes. **Tracked.** Published to Creality.
- `gcode/` — slicer output for cost analysis. **Gitignored** — regenerate as needed.
- `macros/` — FreeCAD `.FCMacro` files (parametric edits, regenerations).
- `scripts/audit_parametric.py` — pre-commit audit; run before any commit that touches FCStd.

### Before any commit that touches FCStd

```bash
python3 scripts/audit_parametric.py
```

If it reports violations, fix them in FreeCAD via a `macros/*.FCMacro` change — never by editing raw sketch coordinates or FCStd XML.

## Print notes (placeholders — fill from the successful test print)

| Setting | Value |
|---------|-------|
| Material | _PLA for test, ASA for production — confirm_ |
| Layer height | _e.g. 0.2 mm_ |
| Nozzle | 0.4 mm |
| Walls / perimeters | _TBD_ |
| Infill | _TBD_ |
| Orientation | Sheet flat on plate |

Update this table once the working print profile is captured.
