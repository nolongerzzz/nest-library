# nest-library

The STLs behind the Library catalog in
[nest-optimizer](https://github.com/nolongerzzz/nest-optimizer)
(`app-library.js`). The page fetches each row straight from this repo's `main`
branch:

    https://raw.githubusercontent.com/nolongerzzz/nest-library/main/<file>

so a file here is live the moment it is on `main`, and a file removed here is a
row that 404s. **Add and remove pieces through the app's Library Save / Delete**,
not by hand: a row is two things - its line in nest-optimizer's `app-library.js`
and its file here - and Save / Delete write both, in the order that can never
leave a row without its file (see nest-optimizer's `docs/LIBRARY-SAVE.md` and
`docs/LIBRARY-DELETE.md`).

Flat names at the repo root, no subdirectories: the catalog addresses files by
bare name, and Save refuses a name with a path in it.

## What is here, and what is not

Every file named by a `CATALOG` row - 40 pieces - plus `traced-lines.json`, the
manifest the traced-line generator writes beside its pieces.

Nine of the 40 are **also** in nest-optimizer's `library/`, because checks
there load them by name (`pin`, `box_closed`, `box_bit_12x8x8`,
`box_hull_80x40x20`, `box_hull_80x40x20-2`, `hinge_knuckle_box`, `USB_bit`,
`tape_on-edge-single`, `v9_mirror_factory`). The two copies are the same bytes,
and `tools/nso_library_delete_test.js` fails if they ever are not. The fixtures
that are *not* catalog rows (`CTH_fixture`, `soften_test_*`,
`tape_on-edge-single-B101_rounded_v8_FINAL`) are only in nest-optimizer.

## Generated pieces - regenerate, do not hand-edit

Several pieces are built by nest-optimizer's tools and byte-checked against
this repo. With this repo cloned beside nest-optimizer (or `NSO_LIBRARY_DIR`
pointing at it), from nest-optimizer:

| pieces | built by | check |
|---|---|---|
| `patch_*` (Skins) | `tools/nso_skin_fine_samples.js` | `npm run skin:fine-fixtures` |
| `traced_*`, `traced-lines.json` | `tools/nso_raster_library_samples.js` | `npm run raster:library` |
| `shape-*`, `cube-*`, `plate-20x20x2`, `bar-20x5x5` | copies of `fixtures/quick/` | `npm run quick:test` |

nest-optimizer's CI checks this repo's `main` out beside the suite, so those
comparisons run on every push there.

### Skins: `patch_*`

`_loose` and `_sandwich` are the relief itself with no rim. `-fine` is the
tighter 0.84 mm pitch (0.42 mm channel, one 0.4-nozzle extrusion line) against
the shipped 1.2 mm default. All 30 x 30 mm except the loose ring, which is
2 x rOut = 10 x 10. The four `_bordered` washers that used to ship are gone -
the bordered construction bonds to a stacked part at 2.857x the contact of a
true flat line.

### Traced Lines: `traced_cross_23-67`, `traced_parallel_113`, `traced_hand_stroke`

Built from real images in nest-optimizer's `fixtures/raster-real/`. All three
are a one-extrusion-line lattice (0.42 mm walls), so they are non-solid for the
reason the loose patches are. `traced_cross_23-67` is the piece that found the
crossing-bridge bug.

### Stock Blanks and the fast Test Fixtures

`shape-*` are the eight `NSO_Stock` primitives, all sized from one target (a
10 mm piece with a 1 mm wall), built at 32 segments. `cube-6mm`, `cube-9mm`,
`cube-12mm`, `plate-20x20x2` and `bar-20x5x5` are twelve triangles each. All
thirteen rest on z = 0; `box_open` and `box_closed` do not.

## Not CSG-safe: `box_closed.stl`

An overlapping-shell concatenation (a tray and a lid slab overlapping the wall
tops by 0.1 mm): watertight per shell and slices correctly, but do not run
Join or Subtract on it expecting a clean result. Kept as is because
nest-optimizer's checks measure against exactly this layout.
