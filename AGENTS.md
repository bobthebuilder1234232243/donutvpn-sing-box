# Rules for AI agents and contributors

This repository is **donutvpn-sing-box**: a fork of [sing-box](https://github.com/SagerNet/sing-box)
with the features DonutVpn adds. These rules hold for every change, by a person or an AI agent. The
step-by-step procedure for adding a feature or fixing a bug is the skill in
[`.agents/skills/donutvpn-feature/SKILL.md`](.agents/skills/donutvpn-feature/SKILL.md).

## 1. Read the DonutVpn docs first

Before any change, read **all** of [`docs/donutvpn/`](docs/donutvpn/index.md): the index, every page
under `features/`, `app_considerations.md` and the bug-fix log in `bugfix/`. They say what DonutVpn
already has, how it works and why. Do not start from the code alone: most "missing" features
already exist under another name.

## 2. Stay close to upstream sing-box

The fork has to follow upstream sing-box releases. Every line changed in an upstream file is a
line that conflicts at the next rebase.

- Put DonutVpn code in new files named `h_*.go`, next to the code they extend.
- When an upstream file must change, change as few lines as possible, and mark every added or
  changed line with `//H` at the end of the line.
- Do not reformat, rename or move upstream code.
- Optional features with large dependencies go behind a build tag (`with_<feature>`), off in the
  Makefile unless asked otherwise.
- A dependency that needs a fix becomes a fork under `replace/` (a git submodule of
  `github.com/donutvpn/<repo>`), with the change on its own branch, and a `replace` line in both
  `go.mod` and `test/go.mod`.

## 3. Improve what exists, do not add a second one

If a feature overlaps something DonutVpn or upstream already has, extend and fix the existing code
and its options instead of adding a parallel one. For example, more TLS fragmentation methods go
into `tls_fragment`, a new URL-test mode into `urltest`, a new WARP tunnel into the `warp`
endpoint. Keep existing configs loading: new options are additions, and old ones keep their
meaning.

## 4. Base every change on the latest donutvpn-sing-box

Every pull request is rebased on the latest `v5` branch of
[donutvpn-sing-box](https://github.com/donutvpn/donutvpn-sing-box) before it is opened, and again
before it is merged. No merge commits from `v5` into a branch; rebase.

## 5. Test it

- Tests for DonutVpn code go in `h_*_test.go` files, with `TestH_` names.
- A bug fix comes with a test that fails without the fix and passes with it. Check both.
- A feature that Xray also has (XHTTP, mKCP, REALITY, VLESS encryption, finalmask, ...) is tested
  against the latest Xray release and the 26.3.x line (`DONUTVPN_XRAY=path:path`, see
  `h_xray_interop_test.go`).
- Tests that need the internet (WARP, Psiphon, ...) skip unless their variable is set
  (`DONUTVPN_WARP_E2E=1`).
- Build and test with the Makefile tags:
  `go test -tags "$(sed -n 's/^TAGS ?= //p' Makefile)" -ldflags "$(cat release/LDFLAGS)" ./...`,
  and once more with the optional tags of the feature you touched.
- A fix ported from another fork is checked first: reproduce the bug here before taking the fix.

## 6. Document it

Every feature DonutVpn adds, and every change to one, is documented in `docs/donutvpn/`, in English
and in Persian:

- A page pair `docs/donutvpn/features/<category>/<feature>.en.md` and `.fa.md`, where the category is
  `protocols/`, `transports/`, `dns/`, `groups/`, or the `features/` folder itself for the rest.
  Follow the sections of the existing pages: what it is, why use it, how it gets past filtering,
  how it works, configuration (an example, then a table of every field), notes, see also. The
  Persian page has the same content in the same order, inside `<div dir="rtl">`.
- One line for it in `docs/donutvpn/index.md` and in `docs/donutvpn/index.fa.md`, under the
  category heading.
- A line for it in the feature list of the [README](README.md).
- A bug fix gets an entry at the top of `docs/donutvpn/bugfix/v5.en.md` and `v5.fa.md`: what
  happened, the fix, and the commit id.
- Anything donutvpn-app or donutvpn-core has to change or know goes in
  `docs/donutvpn/app_considerations.md`.
- Every relative link must resolve; check after moving or adding pages.

## 7. Commit it

- One commit per feature or fix, with a message `type(area): what it does` (`feat`, `fix`,
  `docs`, `test`, `build`, `refactor`, `chore`) and a body that says why.
- No lines naming an AI agent as author or co-author.
- Commits in a `replace/` fork go on that fork's branch; bump the submodule in a separate commit.
