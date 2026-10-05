# Personal Obsidian reader fork

This is `tparsons9/obsidian-zotero-reader` on its persistent `personal` branch.
The owner repository is `duanxianpi/obsidian-zotero-reader`. The original annotation
profile contribution remains separately on `feat/annotation-profiles`; preserve
that PR branch. Upstream build documentation lives in [README.md](README.md).

The adopted owner base is `e6dfec2857b1e008a416832e1bd9df868513b035` from the owner's
`zotflow` branch. Our annotation feature commit is
`6fb7358b57d3f73444ae39fdc25017e9e9027394`. The parent's reminder workflow monitors
owner `master`, which can diverge from the `zotflow` integration branch. Inspect
both the owner changes and the reader pin from a ZotFlow release before merging;
an alert does not mean a commit has been adopted.

## Agent and maintenance rules

- Read this file and `README.md` before editing. If upstream introduces an
  `AGENTS.md`, read it too and apply its coding guidance with these personal rules.
- Work on `personal` or review branches based on it. Prepare upstream contributions
  separately from the owner's intended base. Do not publish releases from this fork.
- Preserve the annotation API below and its instance isolation and creation-only
  tagging behavior. Labels and profile IDs are host configuration, not new fields
  in serialized annotations.
- Keep personal documentation here and the small `AGENTS.override.md` tracked.
  Allow the README to follow upstream unchanged.
- Run `npm test` for reader code changes. For coordinated host changes, run the
  parent's tests, personal tooling tests, and full `npm run build:ci` as well.
- Publish reviewed reader commits before the parent records their exact gitlink.
  Never choose an entire side of a submodule conflict without inspecting the pin.
- The parent fork owns reminders, adoption tracking, and installation into
  `~/vaults/tanners-vault`; standalone reader work does not install into the vault.

Git conflict reuse is enabled locally with `rerere.enabled=true` and
`rerere.autoupdate=false`. Repeat `git config --local rerere.enabled true` and
`git config --local rerere.autoupdate false` after a fresh clone. Review reused
resolutions and run tests before staging them. Source, tests, package configuration,
and a future upstream file with one of our personal filenames can still conflict.

## Annotation profile configuration (Obsidian host)

`initReader` accepts an optional `annotationProfileConfig`:

```js
{
    profiles: [{
        id: "research",
        name: "Research",
        palette: [{ id: "method", color: "#ffd400", label: "Methodology" }]
    }],
    activeProfileId: "research",
    autoTag: false
}
```

The host validates nonempty palettes and distinct normalized hex colors before
sending configuration. The reader copies it into instance state. Missing
configuration preserves the original Zotero palette and tool defaults.
`setAnnotationProfileConfig(config)` updates it without changing annotations;
`annotationProfileChanged` emits `{ profileId }` when the user chooses a profile.
The host owns settings persistence and reconnect replay.

Menus, selection popups, keyboard shortcuts and sidebar color filters use the
instance palette. Ink/text retain extra black. A removed tool color falls back
to the first available color; existing annotations retain their colors and tags.
With `autoTag`, genuinely new interactive annotations receive the chosen entry's
label as an ordinary tag before history/debounced save. Refresh, import, edit,
undo/redo and structural recreation do not apply creation defaults. Labels and
profile IDs are never added to serialized annotations.

Run `npm test` for palette isolation, creation-time tagging, undo/redo and native
selector keyboard regression checks. The ZotFlow host has a local-only live test
at `tests/live/annotation-profiles.live.mjs` for the coordinated API and UI.
