# The stack file (`.trafolo.json`)

A layout file holds 2D shapes only. The stack file says where each layer sits in the third
dimension. It is a small JSON file next to the layout, with the same name and the ending
`.trafolo.json`:

```
spiral_single.gds
spiral_single.trafolo.json
```

When TRAFOLO imports `spiral_single.gds` (or `.oas`) it reads `spiral_single.trafolo.json` from the
same folder and fills in the layer dialog. Without a stack file the dialog opens with empty values
and you type them in, or use a preset.

The **Export for TRAFOLO** macro writes this file for you. You can also write or edit it by hand.

## Example

```json
{
  "version": 1,
  "units": "um",
  "source": "KLayout",
  "layers": [
    {"layer": 1,  "datatype": 0, "name": "M1_BOTTOM_CU", "offset": 0.0,  "thickness": 10.0, "role": "coil", "enabled": true},
    {"layer": 2,  "datatype": 0, "name": "VIA_CU",       "offset": 9.5,  "thickness": 16.0, "role": "coil", "enabled": true},
    {"layer": 3,  "datatype": 0, "name": "M2_TOP_CU",    "offset": 25.0, "thickness": 10.0, "role": "coil", "enabled": true},
    {"layer": 10, "datatype": 0, "name": "MAG_CORE",     "offset": 12.0, "thickness": 5.0,  "role": "core", "enabled": true},
    {"layer": 1,  "datatype": 2, "name": "PAD_OPEN",     "offset": 0.0,  "thickness": 0.0,  "role": "other", "enabled": false}
  ]
}
```

## Fields

| Field | Type | Meaning |
|---|---|---|
| `version` | integer | Format version. Always `1`. |
| `units` | `"mm"` or `"um"` | Unit of every `offset` and `thickness` in the file. |
| `source` | text | Free text, for example the program that wrote the file. Not used by the import. |
| `layers` | list | One entry per layer, see below. |

Per layer:

| Field | Type | Meaning |
|---|---|---|
| `layer`, `datatype` | integers | GDSII layer and datatype numbers. Both must match: `1/0` and `1/2` are different layers. |
| `name` | text | Display name. Optional; a `.lyp` file or the layout's own layer names are used otherwise. |
| `offset` | number | Height of the layer's bottom face. |
| `thickness` | number | Thickness, measured upwards from `offset`. `0` imports the layer as a flat face instead of a solid. |
| `role` | text | `coil`, `core`, `bobbin` or `other`. Sets the geometry type of the imported solids. |
| `enabled` | true / false | `false` keeps the layer in the dialog but does not build it. |
| `color` | `[r, g, b]` | Optional colour used in TRAFOLO, 0 to 255 per channel. |

## Coordinates

- The layout's own database unit is converted to millimetres, so a layout drawn in micrometres
  arrives at the right size. The `units` field only concerns the stack values.
- The drawing plane (layout X and Y) becomes TRAFOLO's X–Z plane. The stack runs along TRAFOLO's +Y
  axis: `offset` and `thickness` are Y values.
- Overlap layers that must become one solid. A via that ends exactly where the copper begins gives
  two touching solids that do not fuse; in the examples the via starts 0.5 µm inside the metal below
  and ends 0.5 µm inside the metal above.

## Layer names and colours

If a KLayout layer-properties file (`.lyp`) with the same name lies next to the layout, TRAFOLO
takes layer names and colours from it. Values in the stack file win over the `.lyp`.
