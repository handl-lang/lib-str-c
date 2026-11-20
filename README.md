# lib-str-c

C implementation of the `str` ports (via the C ffi).

This repo is a **skeleton**: the layout and gate are in place, the
implementation has not landed yet (`src/` holds only `.gitkeep`).

## Layout

```text
src/        the .hnl sources (empty until the implementation lands)
pkg.hnl     package manifest — modules, deps, description, license
ci.sh       bupha entry → scripts/hnl-gate.sh
```

## Gate

```bash
HNL=../host-cli/target/debug/hnl scripts/hnl-gate.sh
```

CI skips the gate while `src/` has no `.hnl` sources.

## License

MIT.
