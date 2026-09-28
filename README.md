# claude-code

Claude Code CLI layer for OpenCharly images.

The `claude-code` candy installs the Claude Code CLI via the official native
installer (`https://claude.ai/install.sh`) — the currently-recommended method. At
image-build time it fetches the platform-native `claude` binary, lets the
installer self-install its launcher under the build user's home, then relocates
the real binary to `/usr/local/bin/claude`.

That relocation is load-bearing: a system path survives the deploy-time
home-volume mount, whereas anything a candy installs under `${HOME}` at build
time is shadowed once a live pod mounts its persistent home volume over
`/home/<user>`. This replaces the deprecated `@anthropic-ai/claude-code` npm
package — a thin launcher that deferred a native-binary download to first runtime
and so failed `claude --version` in hermetic offline image builds.

A CUE-validated tmux terminal profile makes the same CLI available through the
generic local/SSH/nested Charly agent channel, and runtime self-updates are
disabled (`DISABLE_AUTOUPDATER=1`) so the image-owned install stays immutable
across real terminal sessions.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `claude-code` |
| Packages | `curl` (no nodejs) |
| Binary | `/usr/local/bin/claude` |
| Environment | `DISABLE_AUTOUPDATER=1` |
| Agent channel | `agent_provide: tmux`, profile `claude-code` (terminal) |
| Service / port | none |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-dev:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-claude-code:v2026.270.0831'
```

Verifiable offline, both in the built image and on a live deployment:

```bash
ls -l /usr/local/bin/claude
/usr/local/bin/claude --version    # prints the Claude Code banner
```

## Layout

- `charly.yml` — the `claude-code:` candy entity: the `download:`/install
  `plan:` steps, env, the tmux terminal profile, and the `check:` assertions,
  plus the embedded `skill:` entity.
- `CHANGELOG/` — per-CalVer release notes.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-coder:claude-code` — the native-install and relocation
  reference
- Sibling AI CLIs: `/charly-coder:codex`, `/charly-coder:gemini`
- Bundled by: `/charly-hermes:hermes-full-layer`, `/charly-hermes:hermes`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
