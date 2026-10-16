# Troubleshooting and limits

## Import

**The file dialog does not offer `.gds` or `.oas`.**
Update TRAFOLO to 1.43 or newer.

**"Layout contains no polygons".**
The file holds no shapes, or only text labels. Labels are ignored; layers that carry only labels are
skipped.

**A layer is missing in the layer dialog.**
Only layers with shapes are listed. Check the layer and datatype numbers: `1/0` and `1/2` are
separate layers.

**The stack values are not filled in.**
The stack file must sit in the same folder as the layout and carry the same name with the ending
`.trafolo.json`: `design.gds` and `design.trafolo.json`.

**The model is too large or too small by a factor of 1000.**
Offsets and thicknesses are read in the unit given by `units` in the stack file (`mm` or `um`).
The shapes themselves always use the layout's database unit.

**Holes and cell arrays.**
Both are handled. Cell hierarchies and arrays are flattened, overlapping shapes on one layer are
merged, and openings stored as keyhole cuts (the only way GDSII and OASIS can express a hole) come
back as real holes.

## Building the model

**Fuse gives two solids instead of one.**
The parts only touch. Let the via layer overlap the copper above and below by a small amount; see
the examples.

**Assembly finds no terminals.**
TRAFOLO looks for winding terminals on the faces that touch the air box. Draw the terminal traces
to the edge of the device and lock the air box limits to those planes on the **Assembly** page.

**Assembly fails with an intersection.**
Two solids occupy the same volume, typically copper drawn inside a board layer. Leave the board
layer disabled (as in the planar transformer example), or cut the windings out of it in the CAD
editor before giving it a dielectric material.

**A rotated or moved part breaks after import.**
Update TRAFOLO. Versions before 1.42 read some OASIS files with small coordinate errors that could
break the solid on a later rotation. GDSII files were not affected.

## KLayout macro

**No TRAFOLO menu.** See [install.md](install.md#the-menu-does-not-appear).

**Export fails on Linux or macOS after writing the files.**
Untick **Open output folder after export**; opening the folder works on Windows only. The files are
written before that step.

**Layer roles are guessed wrongly.**
The guess only looks at the layer name. Change the role in the dialog before exporting, or edit the
stack file afterwards.
