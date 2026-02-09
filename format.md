### CSV format for datasets (`*.csv`)

The viewer expects all datasets (e.g. `drosophila.csv`, `nephron.csv`, `vitro_*.csv`) to follow this exact structure:

- **Row 1 – per-frame cell counts**
  - Comma-separated **integers**.
  - Each entry gives the **number of cells in that time step (frame)**.
  - The number of columns in this row defines **`totalFrames`**.

- **Row 2 – optional cell-type definitions**
  - If this row contains **both** `:` and `,`, it is interpreted as a **type dictionary** and data then starts on row 3.
  - Format: `id: Name, id: Name, ...`
    - `id` is an integer type code (e.g. `0`, `1`, `2`).
    - `Name` is an arbitrary label for that cell type (no commas inside the name).
  - Any type codes that appear in the data but are not listed here get an automatic name `Cell type <id>`.
  - If row 2 does **not** match this pattern, it is treated as the **first data row**, and there is **no explicit dictionary row**.

- **Data rows – coordinates and optional type**
  - After the header(s), there are \(\sum\) of the counts from row 1 data rows, grouped **contiguously by frame**:
    - First `count[0]` rows → frame 0
    - Next `count[1]` rows → frame 1
    - etc.
  - Each data row is comma-separated:
    - `x, y, z` **or**
    - `x, y, z, type`
  - `x`, `y`, `z` are floating-point coordinates.
  - `type` (if present on the first data row) is a numeric cell-type code:
    - If a 4th column is present on that first data row, **all rows are assumed to have type data**; missing 4th values in later rows default to type `0`.
    - If the first data row has only three columns, all cells are treated as a single default type `0`, and the viewer disables cell-type coloring.

- **Additional notes**
  - No comment or blank lines are supported inside the file.
  - Any extra lines beyond the expected total number of data rows are ignored by the reader.

