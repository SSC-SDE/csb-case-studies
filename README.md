# CodeShouldBehave case studies

Pinned, reproducible reviews of public Rust repositories. Reports were generated
with the published `v0.6.0` image digest
`sha256:53e53a1f6374c58dacf90e1c52eca4130d1c82531f217edb56f31a43e507c075`
and verified with `csb verify`.

These are independent analyses of public commits. They are not endorsements by
the upstream projects and do not claim runtime correctness. Every report warns
that coverage is incomplete because the bounded profile does not evaluate
macros, conditional features, non-Rust files or all dependency forms.

| Case | Exact change | Report | Verify index |
|---|---|---|---|
| [`sharkdp/fd`](https://github.com/sharkdp/fd) | [`2381562`](https://github.com/sharkdp/fd/commit/23815624819c77ab665e19c8e6c28bddbcd96e1d) — refactor pattern types | [`fd.zip`](https://github.com/SSC-SDE/csb-case-studies/releases/download/case-studies-2026-11-21/fd.zip) | `c44a29ed…` |
| [`dtolnay/thiserror`](https://github.com/dtolnay/thiserror) | [`f82a0cf`](https://github.com/dtolnay/thiserror/commit/f82a0cf8f06c71263c2caa26e759f337d2778153) — nested turbofish depth | [`thiserror.zip`](https://github.com/SSC-SDE/csb-case-studies/releases/download/case-studies-2026-11-21/thiserror.zip) | `595505b2…` |
| [`BurntSushi/ripgrep`](https://github.com/BurntSushi/ripgrep) | [`e2b508d`](https://github.com/BurntSushi/ripgrep/commit/e2b508de363c9f16fff2a6bb50e8692287cba015) — custom line terminators | [`ripgrep.zip`](https://github.com/SSC-SDE/csb-case-studies/releases/download/case-studies-2026-11-21/ripgrep.zip) | `25622ee9…` |

## Open a report

```sh
unzip fd.zip -d fd
python3 -m http.server 8080 --directory fd
# open http://localhost:8080/
```

Each archive contains the self-contained review, `manifest.json`,
`artifact.json` and `sources/`. It does not contain the reviewed repositories.

## What the three cases show

- `fd` is a structural refactor: one component, 152 HLD changes and 163 LLD
  changes, including new `PatternType`/`Patterns`/`Matcher` declarations.
- `thiserror` is an internal parser change: no HLD change, 71 LLD changes in
  `parse_token_expr`, with signature/condition/operation changes.
- `ripgrep` is a cross-crate internals change: no HLD change, 25 LLD changes
  across `grep-regex` and `grep-searcher`, with 38 lexical cross-file candidates.

This is the intended split: HLD stays quiet for internal behavior changes, while
LLD and exact source retain the detail a reviewer needs.

## Reproduce

```sh
git clone https://github.com/sharkdp/fd.git
cd fd
BASE=$(git rev-parse 2381562^)
HEAD=$(git rev-parse 2381562)
docker run --rm -v "$PWD:/repo:ro" -v "$PWD/review:/out" \
  -e GIT_CONFIG_COUNT=1 -e GIT_CONFIG_KEY_0=safe.directory -e GIT_CONFIG_VALUE_0=/repo \
  ghcr.io/ssc-sde/csb@sha256:53e53a1f6374c58dacf90e1c52eca4130d1c82531f217edb56f31a43e507c075 \
  review --repo /repo --base "$BASE" --head "$HEAD" --out-dir /out --design auto
```

## Licenses

The reviewed projects retain their own licenses and copyright:

- `sharkdp/fd`: MIT / Apache-2.0.
- `dtolnay/thiserror`: MIT / Apache-2.0.
- `BurntSushi/ripgrep`: MIT / Unlicense.

Reports include limited source spans from those public commits for review
context. Upstream names are used for identification only.
