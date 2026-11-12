# gitui

An Ocean Git TUI inspired by [jjui](https://github.com/idursun/jjui), using the standard library `term/tui` model/update/view runtime.

Requires a current Ocean compiler with `term/tui`, Clang, and Git on PATH.

```sh
ocean check .
ocean build . -o gitui
# From any Git repository:
/path/to/gitui
```

Press `?` for bindings. `j/k` navigate the commit graph, `l` opens details, `p` toggles patch preview, `f` opens working-tree files, `s/u` stage/unstage, `c` commits the index, `b/B` list/create branches, and `o` opens reflog. `/` filters commit messages. Preview scrolls with Ctrl+n/p and Ctrl+d/u. `R` refreshes.

`e` checks out a branch or detached commit; `r` captures the selected source revision and enters destination selection (j/k, Enter, Escape); `s` replays that revision on a detached HEAD, while `b` rebases the current branch from the source revision onward. `C` continues a conflicted rebase and `A` aborts it; `x` cherry-picks; `a` reverts. These display the exact Git command and require typing `yes`. Escape cancels. Git failures appear in the status line; conflicts can be resolved with external Git tools and refreshed.

`--snapshot` prints the initial view without owning the terminal; `--help` prints usage.

This is an initial implementation, not full jjui parity. Git commits replace jj changes and reflog replaces the operation log. Missing workflows include interactive hunk selection, squash/split, conflict resolution, remote operations, configuration, and revset completion. File staging currently uses Git's short-status text format; unusual quoted filenames and rename entries need further handling. Commands currently run synchronously and large repositories may pause during refresh.

The log renders Unicode graph edges and commit nodes, with a revset header and optional preview (`p`). Rebase commands use `git rebase --onto destination source^ source-or-branch`; root revisions and invalid branch/source combinations report Git errors without completing the operation.
