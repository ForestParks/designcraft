# Linked artwork in native working files

By default, native `.designcraft` documents retain an embedded copy of placed artwork, even
when the Links panel records an external source. Existing documents keep this behavior.

To save a smaller working document, open **Window › Links** and select **Keep linked artwork
external on save**, then save. The choice belongs to the document and can be undone. Placed
files that have source paths are then stored as references, without their full originals in
the native ZIP. Artwork without a source path, including an item explicitly set to **Embed**,
stays embedded. Placed audio and video also stay embedded. **Relink** attaches an embedded item to a source file again.

Scripts can choose the same mode:

```text
links.storage {"mode":"linked"}
file.saveAs {"path":"/project/layout.designcraft"}
```

Or set it while saving:

```text
file.saveAs {"path":"/project/layout.designcraft","embedImages":false}
```

The next `file.save` remembers that choice. `embedImages:true` switches back to including
linked originals; `links.storage {"mode":"embedded"}` does the same. `file.saveACopy` accepts
the option without changing the open document's mode, path or saved state.

Source paths are stored relative to the working document where possible, so moving the layout
and its `Links/` folder together keeps them connected. Opening a native file loads its linked
originals into memory for editing and full-resolution export. Changes to a source during the
session still appear as Modified in the Links panel and can be accepted with Update.

If an original cannot be found when opening, the layout opens with a warning and the missing
link can be relinked. There is no saved preview for unresolved artwork in this first version.
If the document still has the original cached in memory but the source has disappeared, a
linked-only save fails instead of discarding that cached copy: use Relink or Embed first.
An unresolved link cannot be embedded until its original is supplied.

Package collects available originals in `Links/`; its native document retains the chosen
storage mode. PDF and print output use the loaded full-resolution artwork. This option reduces
working-file storage and serialization. Encoded originals remain resident for reliable export,
undo and recovery. Decoded screen pixels are loaded on demand and managed separately; see
[display performance](display-performance.md). External artwork reads are limited to 512 MiB
per source file; use embedded storage for larger sources. EPS retains the existing preview
rendering behavior; this change does not add a PostScript interpreter.

Recovery, clipboard/snippets and `file.serialize` remain self-contained. Byte-only opening
(`file.openBytes`, including the web app) has no project folder from which to resolve external
files, so it keeps any embedded data and warns about unresolved artwork instead of reading
paths supplied inside a document. The Links panel option is disabled on the web.

External-link working files use native format 3. Older applications must ask for an update
rather than open the layout with silently missing artwork. Ordinary embedded files remain
format 2; opening older files never converts them to external storage automatically.

Native format 1 layouts retain their original first-baseline calculations when opened. Saves
containing this compatibility setting use format 4, including embedded saves, so older readers
refuse them instead of silently moving text. Current documents without this setting remain
format 2 (embedded) or format 3 (linked). Changing the first-baseline kind in Text Frame
Options adopts the current calculation for that frame. Reconfirming the same kind or changing
unrelated frame options retains compatibility.
