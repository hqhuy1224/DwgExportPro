# DwgExportPro

Batch-export AutoCAD drawings (DWG/DXF) to **PDF** or **PNG** — select a layout for each drawing, process entire folders in batches, and run unattended.

DwgExportPro opens each drawing through AutoCAD COM automation, calculates the actual drawing extents (filtering outliers and abnormal geometry to prevent misplaced blocks or infinite lines from distorting the output), centers the plot area, and exports to PDF or PNG. If the output is blank, the tool automatically retries using fallback methods.

> Built on the original **ScriptPro** batch-processing engine released by Autodesk Developer Network, with a dedicated PDF/PNG export pipeline and a more reliable AutoCAD COM automation layer. See [Credits](#credits).

---

## Features

- **Batch PDF/PNG export** — queue any number of DWG/DXF drawings and export them in a single run.
- **Per-drawing layout selection** — select Model or any Paper Space layout independently for each file.
- **Smart plot-area detection** — calculates the actual drawing bounds for each drawing instead of relying on AutoCAD's fixed `Extents` or `Display` plot-area settings:
  - Collects the bounding boxes of all visible, plottable entities.
  - Filters outliers using the Median + MAD (median absolute deviation) statistical method, preventing misplaced blocks or distant dimensions from shrinking the main drawing.
  - Preserves large entities, such as drawing borders and title blocks, even when their centers are offset from the main cluster.
  - Adds a small margin and centers the drawing within the plot area.
- **Automatic fallback** — if the output is blank (detected using the pixel-ink ratio for PNG files), the tool retries with `Extents`, followed by `Display`, before reporting failure.
- **More reliable AutoCAD automation** — runs on a dedicated STA thread and registers a COM Message Filter to mitigate `RPC_E_SERVERCALL_RETRYLATER` and application-busy errors during long batch-processing sessions.
- **File-verified export status** — a drawing is marked `Done` only when the output file actually exists and contains data, rather than simply because AutoCAD returned without throwing an exception.
- **Attach to or launch AutoCAD** — reuses an existing AutoCAD instance when available and leaves it running afterward; otherwise, launches and manages its own AutoCAD instance for unattended processing.
- **Automatic AutoCAD restart** — restarts AutoCAD after every N drawings to help control memory usage during large batch runs.
- **Reusable project files** (`.bpl`) — save drawing lists and settings for later reuse, including command-line execution.
- **Legacy ScriptPro project import** — reads older ScriptPro (`.scp`) project files to facilitate migration.
- **Command-line and unattended execution** — runs saved projects from batch files or Windows Task Scheduler, with an option to exit automatically when processing is complete.

## System Requirements

- Windows 10/11, 64-bit.
- AutoCAD 2022 or later (the officially tested version range; older versions with COM support may work but are not officially supported).
- .NET 8.0 Desktop Runtime (x64).
- A licensed copy of AutoCAD installed on the machine running the tool.

## Installation

**Portable version:**

Extract the build output and run `DwgExportProUI.exe` directly — no installation required.

## Usage

1. Add drawings using **Add** (individual files) or **Add From Folder** (folder import, optionally including subfolders).
2. Select the layout to export for each drawing in the list, including `Model` or the appropriate paper space layout.
3. Choose **PDF** or **PNG** as the output format.
4. (Optional) Open **Options** to configure timeouts, the AutoCAD restart interval, startup scripts, the output folder, and other settings.
5. Click **Run → Checked** to process checked drawings, or select **Selected** / **Failed** to process the corresponding groups.
6. Review the logs after execution. Each run generates a summary log and a detailed AutoCAD command-line log.

### Command-Line Execution

```bat
DwgExportProUI.exe "C:\Projects\MyProject.bpl" run
DwgExportProUI.exe "C:\Projects\MyProject.bpl" run exit
```

The first command executes the saved project. The second executes the project and exits automatically when processing is complete.

This feature is useful for scheduling automated drawing exports through Windows Task Scheduler or running them from batch files.

## How Plot-Area Detection Works

When exporting drawings from Model Space, DwgExportPro does not rely solely on AutoCAD's `Extents` or `Display` plot-area settings. Both methods can be affected by geometry located far outside the actual drawing, such as misplaced imported blocks or points accidentally placed at extremely large coordinates.

Instead, the tool follows these steps:

1. Iterates through all entities in the active drawing space and retrieves their bounding boxes through COM. It skips infinite-line entities such as `XLINE`/`RAY`, hidden or frozen layers, non-plottable layers, and invalid bounding boxes.
2. Calculates the median of entity centers and the MAD (median absolute deviation). This robust measure of dispersion is less sensitive to a small number of extreme outliers.
3. Removes entities whose centers are approximately more than 20 times the MAD away from the median, unless they are large entities, such as title blocks or borders, to avoid discarding important drawing components.
4. Builds the final bounding box from the remaining entities, adds a small margin, and sets the plot area to `Window` to center the drawing on the output page or image.
5. If the output is blank, falls back to `Extents`, followed by `Display`, and retries the export.

This approach helps produce more consistent batch exports, even for drawings containing problematic geometry, while reducing the need for manual adjustment on individual files.

## Building from Source

Requirements:

- Visual Studio 2022.
- .NET 8.0 SDK.

Open the solution in Visual Studio, restore the NuGet packages, and build the solution.

## Project Structure

| Project | Description |
|---|---|
| `DwgExportProUI` | Main WPF application containing the UI, entry point, and navigation components. |
| `DrawingListUC` | Drawing list user control responsible for drawing management, project management, AutoCAD COM automation, and the PDF/PNG export engine. |

## Credits

DwgExportPro is derived from the original **ScriptPro** source code, initially released by Autodesk Developer Network:

- **.NET 8.0 modernization (base engine)** — Madhukar Moogala.
- **DwgExportPro** — batch PDF/PNG export pipeline, per-drawing layout selection, outlier-filtered plot-area detection, and AutoCAD COM reliability improvements, including a dedicated STA thread, COM Message Filter, and file-verified export status.

Icons: [FatCow Free Icons](http://www.fatcow.com/free-icons).

## License

This project is derived from the ScriptPro source code, originally released by Autodesk, Inc. under terms permitting the use, copying, modification, and distribution of object-code versions, provided that the original copyright notice and disclaimer are preserved.

See the [LICENSE](LICENSE) file for the full terms applicable to both the inherited ScriptPro code and the modifications made in this repository.

**Original source copyright © 2010–2026 Autodesk, Inc.** The software is provided "AS IS", without warranty of any kind.

Modifications and the DwgExportPro name © 2026 the project author, distributed under the same terms.
