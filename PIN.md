# PIN — DeepSeek Harness v0.2.2-rc.1

This repository is a **durable, pinned copy** of DeepSeek Harness, held under the
`empire-mind` organisation so the estate can rebuild the exact same source even if
the upstream repository disappears.

The single source of truth is the commit below. The branch
`pin/v0.2.2-rc.1` exists only to carry this file; **every line of buildable code
lives at the pinned commit, unmodified.**

## Pinned artefact

| Field | Value |
| --- | --- |
| Upstream repository | https://github.com/deepseek-ai/deepseek-harness |
| Durable fork | https://github.com/empire-mind/deepseek-harness |
| Pin branch | `pin/v0.2.2-rc.1` |
| Pinned commit (full SHA) | `fcc0ad462172522aaf5c93a7b2e01f6b9f0b0159` |
| Pinned commit (short SHA) | `fcc0ad4621` |
| Commit date | 2026-11-04 16:40:00 +0800 |
| Commit subject | `Merge pull request #7873 from deepseek-harness/merge/release-dsh-0.2.2-rc.1-to-master` |
| Version (`package.json`) | `0.2.2-rc.1` |
| Required Node.js | `>=24.9.0` (declared in `engines.node`) |
| Required pnpm | `11.28.5` (declared in `packageManager`) |

## Rebuild from zero

A clean machine needs only Node.js `>=24.9.0`, pnpm `11.28.5`, and git. Run the
following in order — each step assumes the previous one succeeded:

```sh
# 1. Clone the durable fork at the pin branch, then check out the exact commit.
git clone --branch pin/v0.2.2-rc.1 https://github.com/empire-mind/deepseek-harness.git dsh-pin
cd dsh-pin
git checkout fcc0ad462172522aaf5c93a7b2e01f6b9f0b0159

# 2. Install dependencies. ("prepare" runs the lefthook installer — see Known issues.)
pnpm install

# 3. Build everything.
pnpm run build

# 4. Start the harness web GUI.
pnpm dsh web --no-open
```

Step 4 leaves the server running in the foreground and does not launch a browser;
open the URL it prints (default `http://127.0.0.1:3080`). Add `--port <n>` to move
it off the default port.

## Known non-fatal issue: lefthook postinstall

`pnpm install` runs the repository's `prepare`/postinstall hook, which tries to
install lefthook via `node scripts/install-lefthook.mjs`. On machines that already
manage git hooks with a global `core.hooksPath` (for example
`git config --global core.hooksPath ~/.config/git/hooks`), the installer
deliberately refuses to take ownership of that path and exits with:

```
refusing to replace user-owned core.hooksPath (<source>).
```

This is **harmless and expected**. It is a guard rail, not a build failure: the
installer declines to overwrite a hooks directory the user owns, so no hooks are
installed into this clone. The build is unaffected and still passes all 10 gates
of `pnpm run build`. Do not try to "fix" it by deleting your global
`core.hooksPath`. If repository hooks are genuinely required, scope the override
to this clone instead:

```sh
DSH_LEFTHOOK_ALLOW_HOOKS_PATH_OVERRIDE=1 pnpm install
```

## Verifying the pin

On any clone, confirm the checked-out commit matches the pin exactly:

```sh
test "$(git rev-parse HEAD)" = "fcc0ad462172522aaf5c93a7b2e01f6b9f0b0159" && echo "PIN OK" || echo "PIN MISMATCH"
```

The expected full SHA is
`fcc0ad462172522aaf5c93a7b2e01f6b9f0b0159`. To prove the durable fork actually
serves that commit — without trusting a local tracking ref — ask the remote:

```sh
git ls-remote https://github.com/empire-mind/deepseek-harness.git pin/v0.2.2-rc.1
```
