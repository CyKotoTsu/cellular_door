### CSV format for datasets (`*.csv`)

The viewer expects all datasets (e.g. `drosophila.csv`, `nephron.csv`, `vitro_*.csv`) to follow this exact structure:

- **Row 1 – per-frame cell counts**
  - Comma-separated **integers**.
  - Each entry gives the **number of cells in that time step (frame)**.
  - The number of columns in this row defines **`totalFrames`**.

- **Row 2 – optional cell-type definitions**
  - If this row contains **both** `:` and `,`, it is interpreted as a **type dictionary** and data then starts on the next row.
  - Format: `id: Name, id: Name, ...`
    - `id` is an integer type code (e.g. `0`, `1`, `2`).
    - `Name` is an arbitrary label for that cell type (no commas inside the name).
  - Any type codes that appear in the data but are not listed here get an automatic name `Cell type <id>`.
  - If this row does **not** match this pattern, it is treated as the **first data row**, and there is **no explicit dictionary row**.

- **Next row – optional scalar definitions**
  - If the next header row starts with `scalars:`, it defines **named scalar values per cell** and data then starts on the following row.
  - Format: `scalars: volume, concentration, age`
    - Scalar names are separated by commas and may contain spaces, but not commas.
    - Example: `scalars: volume, concentration` defines two scalars per cell.
  - This row is **optional**. If it is missing, no scalar data is read.

- **Data rows – coordinates, optional type, optional scalars**
  - After the header(s), there are \(\sum\) of the counts from row 1 data rows, grouped **contiguously by frame**:
    - First `count[0]` rows → frame 0
    - Next `count[1]` rows → frame 1
    - etc.
  - Each data row is comma-separated:
    - `x, y, z`
    - `x, y, z, type`
    - `x, y, z, s1, s2, ... sN`
    - `x, y, z, type, s1, s2, ... sN`
  - `x`, `y`, `z` are floating-point coordinates.
  - `type` (if present) is a numeric cell-type code:
    - If **no `scalars:` header** is present and the first data row has at least 4 columns, the 4th column is treated as `type` for all rows; missing 4th values in later rows default to type `0`.
    - If a type dictionary header **and** a `scalars:` header are both present, the 4th column is `type` and any remaining columns in the row are scalars.
    - If a `scalars:` header is present **without** a type dictionary, no column is treated as `type`; all cells are treated as a single default type `0`.
  - `s1, s2, ... sN` are scalar values (one column per scalar defined in the `scalars:` header):
    - If a `scalars:` header is present and there is **no type dictionary**, scalar values start in column 4 (after `x, y, z`).
    - If both a type dictionary and a `scalars:` header are present, scalar values start in column 5 (after `x, y, z, type`).
    - Missing scalar values in a row are treated as undefined and do not contribute to color scaling.

- **Additional notes**
  - No comment or blank lines are supported inside the file.
  - Any extra lines beyond the expected total number of data rows are ignored by the reader.

### Examples
- **Just positions** (no types):
  - Row 1: `100,100,100` (3 frames, 100 cells each)
  - Data rows: `x, y, z`
- **Types only** (no scalars):
  - Row 1: `100,100,100` (3 frames, 100 cells each)
  - Row 2: `0: Uncommitted NPCs, 1: Ureteric bud`
  - Data rows: `x, y, z, type`
- **Scalars only** (no types):
  - Row 1: `100,100,100`
  - Row 2: `scalars: volume, concentration`
  - Data rows: `x, y, z, volume, concentration`
- **Both types and scalars**:
  - Row 1: `100,100,100`
  - Row 2: `0: Type A, 1: Type B`
  - Row 3: `scalars: volume, concentration`
  - Data rows: `x, y, z, type, volume, concentration`

