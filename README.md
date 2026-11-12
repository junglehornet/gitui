# gitui

An Ocean Git TUI inspired by [jjui](https://github.com/idursun/jjui), using the standard library `term/tui` model/update/view runtime.

Requires a current Ocean compiler with `term/tui`, Clang, and Git on PATH.

```sh
ocean check .
ocean build . -o gitui
# From any Git repository:
/path/to/gitui
```

Press `?` for bindings. `j/k` navigate the commit graph, `l` opens details, `p` toggles patch preview, `f` opens working-tree files, `s/u` stage/unstage, `c` commits the index, `b/B` list/create branches, and `o` opens reflog. `/` filters commit messages. Preview scrolls with Ctrl+n/p and Ctrl+d/u (half a page). `R` refreshes.

`e` checks out a branch or detached commit; `r` captures the selected source revision and enters destination selection (j/k, Enter, Escape); `s` replays that revision on a detached HEAD, while `b` rebases the current branch from the source revision onward. `C` continues a conflicted rebase and `A` aborts it; `x` cherry-picks; `a` reverts. These display the exact Git command and require typing `yes`. Escape cancels. Git failures appear in the status line; conflicts can be resolved with external Git tools and refreshed.

`--snapshot` prints the initial view without owning the terminal; `--help` prints usage.

This is an initial implementation, not full jjui parity. Git commits replace jj changes and reflog replaces the operation log. Missing workflows include interactive hunk selection, squash/split, conflict resolution, remote operations, configuration, and revset completion. File staging currently uses Git's short-status text format; unusual quoted filenames and rename entries need further handling. Selection changes load patches asynchronously only while preview or details are visible; stale results are discarded. Repository-list refreshes still run synchronously and may pause in large repositories.

The log renders Unicode graph edges and commit nodes, with a revset header and optional preview (`p`). Selection covers the entire revision, including its description; j/k skip graph continuation lines. The viewport keeps the selected revision visible, with a fixed status and contextual shortcut footer. Full-screen diffs support j/k scrolling and q to return; help also closes with q. Rows are clipped to terminal width and the layout adapts to terminal height. Rebase commands use `git rebase --onto destination source^ source-or-branch`; root revisions and invalid branch/source combinations report Git errors without completing the operation.

## Git operation menu

Press `g` to open the operation menu. The search field is focused immediately. Type to filter command names and Git arguments (case-insensitive), use arrows or Ctrl+n/p to select, then Enter. Backspace edits the query; Ctrl+u clears it; Escape closes the menu. Letters including j/k remain search text.
Guided fields cover push/upstream/force-with-lease, fetch/prune, pull, remote
add/rename/remove/URL changes, branch switching/creation/deletion/merging,
staging and commits, stash, tags, rebase/cherry-pick/revert recovery, conflict
side selection, reset, worktrees, submodules, and repository configuration.
Every menu command displays the assembled Git command and requires `yes`.
Execution runs asynchronously and opens a scrollable output view on completion.

Press `:` for other Git arguments, such as `log --author 'Your Name'`.
Arguments support quotes and backslash escapes; shell pipelines, substitutions,
and redirections are not executed. The `git` prefix is supplied automatically.
Commands use the current repository and existing Git credential helpers.
Interactive terminal prompts and editors are disabled: authentication requiring
terminal input, interactive patch selection, and interactive rebase editing are
not implemented. Use the guided commit message and recovery actions where available.
The application waits for an active command to finish before accepting more keys.

Visual defaults were compared with upstream jjui commit `ba61a80`: magenta revset
label, bold green expression, bright-black selection, magenta status badge, and
an automatic right/bottom preview split based on terminal aspect ratio.
Exact screenshot parity is not established; Git metadata and operation screens
also differ from Jujutsu's change model.

The Git menu uses jjui’s centered, square-bordered frame dimensions with a dimmed backdrop and a visible software caret in the focused search field. The log uses jjui’s single-row footer and lowercase revset label. Pixel-perfect parity across all screens is still unverified.

Formatting reference: jjui `ba61a80`, `internal/config/default/default.toml`,
`internal/ui/status/status.go`, and `internal/ui/git/git.go`. `theme.oc` defines
shared default-dark theme roles. The menu title uses ANSI colors 230/62,
selected entries use bold cyan without a marker, matches use bold magenta,
help headings use bold green, and footer bindings use magenta keys with dimmed
bullet separators and a complete `? more` hint when space is limited.
The focused menu field is labeled `Filter: `, matching upstream.
These settings target jjui's default dark theme, not user themes or its dynamic
terminal-background blending. Graph and diff content remain Git-derived;
Jujutsu's configured graph/diff styles are not imported.

The graph is rendered from Git parent IDs in topological order, rather than by
substituting Unicode into `git log --graph`. It uses two-column lanes, rounded
merge/join junctions, two-line revision entries, a green `@` for HEAD, white `○`
commit nodes, yellow authors, cyan dates, magenta refs, and blue commit IDs.
Unloaded parents end with `~` markers. Git has no Jujutsu change IDs or immutable
revision classification, so those fields and diamond markers are not fabricated.
