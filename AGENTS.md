# lib-str-c

C/ICU backend of the `str` contract — the native-speed implementation,
written in Handl over the C FFI (`ffi.call`).

## Layout

| path | role |
| --- | --- |
| `src/index.hnl` | the backend implementation |
| `src/index.test.hnl` | the .hnl test suite |
| `pkg.hnl` | manifest — deps `str` + `str-handl` |
| `scripts/hnl-gate.sh`, `workflows/gate.yaml` | the gate / bupha entry |

## Contract

- Hybrid, per-op: libc byte ops (`memcmp`/`memmem` via
  `ffi.call("libc.so.6", ...)`) for contains/count/find/rfind/split/
  replace — UTF-8 is self-synchronising so byte search is
  scalar-exact; `find`/`rfind` map byte offsets back to scalar
  indices.
- ICU4C case mapping (`u_strToUpper_N`/`u_strToLower_N` on
  `libicuuc`, versioned symbols discovered like `unicode-c`) with a
  UTF-16 marshal codec vendored from `unicode-c`; falls back to
  per-char `char.to_upper`/`to_lower` when ICU is absent. Matches
  native `str::to_uppercase`/`to_lowercase`, including Final_Sigma.
- Char-index slicing (`char_at`/`slice`/`split_at`/`truncate`) via a
  UTF-8 scalar-offset table + `bytes.slice`/`utf8_decode`.
- Sequential Unicode algorithms (`trim*`, `reverse`, `is_*`,
  `capitalize`, `title_case`, `snake/camel/pascal/kebab_case`,
  `levenshtein`, `similarity`, `lines`, `split_whitespace`,
  `slugify`, `pad_*`, `word_wrap`) delegate to `str-handl`.
- Diagnostics: `icu_available`, `icu_version`.

## Verification

```bash
HNL=../host-cli/target/debug/hnl scripts/hnl-gate.sh
```

## License

MIT.

<!-- myco:agent-awareness -->

## Agent awareness

- Session start / any work discussion: run `myco status --prompt`
  before proposing next steps — know the board first.
- User describes new work → offer to capture it (`myco todo add`,
  `issue new`, `proposal new`). Don't wait to be asked.
- Picking up a task: `myco resume <ID>` is the briefing.
- Wrapping up: `myco handoff <ID>` anything resumable; keep statuses
  honest (claimed → in-progress → blocked/done).

<!-- /myco:agent-awareness -->
