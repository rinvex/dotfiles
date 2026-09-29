# Rinvex Dotfiles

Based on [Mathias’s dotfiles](https://github.com/mathiasbynens/dotfiles)

No extensive documentation included at the mean time, just use the original repo if you need more control.

These dotfiles prepared for internal use only, without much attention for general public usability, so use of your own risk.

1. Copy all files to your mac home directory.
2. Execute osx.sh file in your terminal to modify system preferences.
3. Install oh-my-zsh: `sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`
4. Copy oh-my-zsh theme: `mkdir -p ~/.oh-my-zsh/custom/themes && curl -fsSL https://raw.githubusercontent.com/rinvex/dotfiles/master/spaceship.zsh-theme -o ~/.oh-my-zsh/custom/themes/spaceship.zsh-theme`
5. Copy `Spectacle-Shortcuts.json` to `~/Library/Application Support/Spectacle/Shortcuts.json`.
6. Done!

## `bin/new-worktree`

Fork a fully isolated, Herd-served git worktree off any project in one command
(on `PATH` via `.zshrc`). Used directly, or through the Warp tab configs in `warp/`.

    new-worktree <project_repo> <base_branch> <feature_name> [launch_cmd] [--fresh] [--no-open]
    new-worktree ls
    new-worktree dev [name]            # node sites: dev server on the worktree's port
    new-worktree rm <name|path> [--force]

What it sets up, per stack:

| | Laravel | Node / Astro |
|---|---|---|
| checkout | `~/Worktrees/<category>/<project>/<slug>` on `feature/<slug>` | same |
| URL | `https://<slug>.<project>.test` (Herd link + TLS) | `https://<slug>.<project>.test` → Herd proxy → `127.0.0.1:<port>` |
| `.env` | copied, then `APP_URL`, `ASSET_URL`, `SESSION_DOMAIN`, `SESSION_COOKIE`, `CACHE_PREFIX`, `REDIS_PREFIX` namespaced | `.env` and `.dev.vars` copied |
| database | sqlite: file cloned · mysql/pgsql: `wt_<project>_<slug>` filled from the project's data (`--fresh` = migrate + seed) | — |
| dependencies | `vendor/` and `node_modules/` APFS-cloned, then `composer install` + lockfile-respecting `npm/pnpm/yarn/bun` install | same |
| files | `storage/app` copy-on-write clone, `storage:link`, Vite build | — |
| teardown | `rm`: unlink/unsecure, drop database, remove worktree, delete branch if merged | `rm`: unproxy, remove, delete branch |

Monorepos whose app lives in a tracked `site/` (or `app/`, `web/`) subfolder are detected
automatically, and any path inside a repo resolves to that repo. `~/Worktrees/.registry.tsv`
records every worktree for `ls`, `dev` and `rm`.

