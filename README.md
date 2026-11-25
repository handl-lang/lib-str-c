# lib-str-c

C/ICU backend of the `str` contract — the native-speed implementation,
written in Handl over the C FFI (`ffi.call`).

## Architecture

Hybrid, per-op:

- **libc byte ops** — `memcmp`/`memmem` via `ffi.call("libc.so.6", ...)`
  for `equals`-adjacent prefix/suffix checks, `contains`, `count`,
  `find`, `rfind`, `split`, `replace`, `replace_first`. UTF-8 is
  self-synchronising, so byte search is scalar-exact; `find`/`rfind`
  map byte offsets back to scalar indices.
- **ICU4C case mapping** — `u_strToUpper_N`/`u_strToLower_N` on
  `libicuuc` (versioned symbols discovered like `unicode-c`), with
  UTF-16 marshal codec vendored from `unicode-c`. Matches native
  `str::to_uppercase`/`to_lowercase`, including Final_Sigma. Falls back
  to per-char `char.to_upper`/`char.to_lower` when ICU is absent.
- **Char-index slicing** — `char_at`/`slice`/`split_at`/`truncate` use a
  UTF-8 scalar-offset table + `bytes.slice`/`utf8_decode`.
- **Delegated** — the sequential Unicode algorithms (`trim*`, `reverse`,
  `is_*`, `capitalize`, `title_case`, `snake/camel/pascal/kebab_case`,
  `levenshtein`, `similarity`, `lines`, `split_whitespace`, `slugify`,
  `pad_*`, `word_wrap`) delegate to `str-handl`, the pure-handl
  reference implementation over the `char` floor.

Diagnostic extras: `icu_available`, `icu_version`.

## Layout

```text
src/        the .hnl sources (index.hnl + index.test.hnl)
pkg.hnl     package manifest — modules, deps, description, license
workflows/gate.yaml       bupha entry (handl-gates) → scripts/hnl-gate.sh
```

## Gate

```bash
HNL=../host-cli/target/debug/hnl scripts/hnl-gate.sh
```

## License

MIT.
