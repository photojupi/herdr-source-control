# Source Control for Herdr

A Git panel for the [Herdr](https://herdr.dev) desktop app, modeled on VS Code's
Source Control view. It follows the focused terminal's repository: stage and
unstage files or single lines, commit (amend, sign-off, commit & push), push,
pull and sync, manage branches, stashes, tags and worktrees, resolve merge
conflicts, and browse a commit graph with branch, tag and remote badges.

![Source Control](preview.png)

## Install

Ask your agent in Herdr:

> add herdr.dev/mods/photojupi/herdr-source-control

Or by hand. Herdr's own layouts and mods are read-only, so the wiring goes
into your own copies:

1. Use a personal layout based on `main` (only `main` has the `panel-body`
   stack and the `tabs` block this wiring needs). If you are on the shipped
   `main`, clone it (this also switches to it):

   ```sh
   herdr-app do layout.clone '{"name":"main","as":"my-main"}'
   ```

2. Copy `mods/source-control` into `~/.config/herdr-app/mods/`. On its own
   this shows nothing: the mod appears only where a layout puts it.
3. Clone the shipped tab bar and edit two lines in
   `mods/tabs-source-control/main.js`:

   ```sh
   herdr-app do mod.clone '{"name":"tabs","as":"tabs-source-control"}'
   ```

   Replace

   ```js
   const enabled = shared("workspace.panels") ?? ["files", "changes"];
   ```

   with

   ```js
   const enabled = [...(shared("workspace.panels") ?? ["files", "changes"]), "source-control"];
   ```

   and right after the Changes button line

   ```js
   .when(enabled.includes("changes"), el => el.child(panelButton("changes", "Changes", "mods/changes/changes.svg")))
   ```

   add

   ```js
   .child(panelButton("source-control", "Source Control", "mods/source-control/icons/branch.svg"))
   ```

   Then run `herdr-app check ~/.config/herdr-app/mods/source-control` and
   `herdr-app check ~/.config/herdr-app/mods/tabs-source-control`; both must
   pass before you do step 4.

4. In your layout file, add a third child to the `stack` with id
   `panel-body`, after `files` and `changes`:

   ```json
   { "id": "source-control", "type": "block", "content": { "mod": "source-control" } }
   ```

   and change the `content.mod` of the block with id `tabs` from `"tabs"` to
   `"tabs-source-control"`.

The tab bar then has a Source Control button next to Files and Changes. It
opens the panel in the same side panel spot as they do, and only one of the
three shows at a time; clicking the lit button again closes it. `RECIPE.md`
has the details.

To keep it always visible instead, skip steps 3 and 4 and add
`{ "id": "source-control", "type": "block", "size": { "px": 360 },
"content": { "mod": "source-control" } }` anywhere in your layout, e.g. as
a child of the root row.

If you installed an earlier version, which docked at the window's right
edge, delete that block (the last child of the root row,
`"mod": "source-control"`) from your layout.

Before installing, know that the panel runs `git` (on this computer, or on a
remote machine for spaces there, including fetch/pull/push), writes temporary
patch files under Herdr's `data/` folder, and reads and writes files in the
repositories it manages.

## Requirements

- Herdr app 0.1.15 or newer
- `git` on PATH (on every machine you use it with)

## Limits

Line-level staging is for ordinary text changes, remote machines support
whole-file operations only, and Git commands time out after 30 seconds. See
[the mod's README](mods/source-control/README.md#limits) for the full list.

## License

Apache-2.0, see [LICENSE](LICENSE).
