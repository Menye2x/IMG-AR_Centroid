# Image Bound & SVG Centroid Detector

A lightweight, browser-based single-file utility for inspecting image dimensions, calculating SVG painted bounds, and finding the visible-coverage centroid of a single closed SVG shape.

The application is designed to run entirely in the browser from one `HTML` file. No upload server or external processing service is required.

## Concept

**Image Bound & SVG Centroid Detector** combines two related image-analysis workflows in one local web utility:

### 1. Aspect Ratio / Bounds

This tab is intended for general image inspection. It detects:

- Intrinsic image resolution.
- Tight painted bounds where applicable.
- Reduced aspect ratio.
- Decimal aspect ratio.

Supported inputs include common browser-decodable raster formats plus SVG. HEIC/HEIF support depends on the browser because the application does not embed a dedicated HEIC decoder.

### 2. SVG Centroid Calculator

This tab focuses on measuring the visible geometry of a single SVG closed shape.

The centroid is calculated from the **shape geometry with stroke removed**, meaning stroke thickness does not move the centroid or change the measured geometry. The visible stroke is treated as a presentation layer centered on the original perimeter.

The calculator reports:

- Coverage width and height.
- Centroid X and Y.
- Normalized centroid coordinates.
- Four coverage-bound corners.
- Coverage aspect ratio.

## SVG Input Rules

Centroid mode uses a strict input contract.

Exactly **one** supported closed shape must be present.

Allowed geometry:

- One `<path>` containing a closing `Z` or `z`.
- One `<rect>`.
- One `<circle>`.
- One `<ellipse>`.
- One `<polygon>`.

The imported SVG is rejected when it contains:

- Multiple drawable geometry elements.
- Multiple supported shapes.
- An open `<path>`.
- Unsupported visible geometry such as text, images, lines, polylines, `<use>`, or similar elements.
- Invalid or incomplete geometry data.
- A hidden/non-painted required shape.

Rejected SVG files do not enter the centroid calculation state. The application keeps the preview empty, disables export controls, and shows a **File Invalid** popup with the reason for rejection.

## SVG Sanitization

Before analysis and export, SVG input is sanitized locally.

The sanitizer removes executable or embedded content such as:

- `<script>`
- `<foreignObject>`
- `<iframe>`
- `<object>`
- `<embed>`

It also removes inline event-handler attributes, JavaScript URLs, and lock/protection-oriented attributes.

This keeps the imported document focused on the allowed SVG geometry and prevents executable content from becoming part of the analysis/export workflow.

## Native SVG Preview

SVG preview uses **native SVG/vector rendering** rather than converting the preview into a raster canvas.

This applies to:

- The main SVG coverage preview.
- The large preview modal.

Benefits:

- Full vector rendering.
- Crisp geometry while zooming.
- No preview-quality loss caused by rasterization.
- SVG shape appearance can be updated without rebuilding the measurement data.

The preview modal supports:

- Fit.
- Reset to 100%.
- Zoom in/out controls.
- Mouse-wheel zoom.
- Drag/pan.
- Maximum zoom of **1000%** for SVG preview.

Raster images continue to use canvas-based preview handling.

## Realtime Appearance Controls

Centroid mode separates measurement data from visual appearance.

### Fill

The fill can be:

- Enabled/disabled.
- Changed through a color picker.
- Changed through HEX input.

### Stroke

The stroke can be:

- Enabled/disabled.
- Changed through a color picker.
- Changed through HEX input.
- Adjusted from **1 px to 10 px**.

Fill and stroke color changes are rendered **realtime** in the native SVG preview.

Stroke-width changes are also rendered **realtime** without recalculating the centroid or coverage metrics.

### Centroid / Guide Appearance

The preview also provides visual controls for:

- Centroid core color.
- Centroid core stroke.
- Centroid outer color.
- Centroid outer stroke.
- Boundary color.
- Boundary stroke.
- Showing/hiding centroid, boundary rectangle, and diagonal guide overlay.

These are presentation controls and do not change the measured centroid geometry.

## Measurement Precision

SVG centroid analysis supports configurable supersampling:

- `1×` — Fast
- `2×` — Balanced (default)
- `4×` — High
- `6×` — Ultra

Higher precision improves alpha-coverage sampling used by the geometry analysis.

## Export

Centroid mode provides two export modes:

### Centroid only

Exports the transparent composition containing:

- Coverage boundary.
- Corner-to-centroid guide lines.
- Centroid markers.

### Centroid + original shape (full)

Exports the original closed shape together with:

- Coverage boundary.
- Corner-to-centroid guide lines.
- Centroid markers.

## PNG Upscaler

Transparent PNG export supports four output scales:

- `x1` — Normal
- `x2` — Sharper
- `x3` — Sharper
- `x4` — Maximum

The upscale process does not simply enlarge an already-rasterized x1 PNG.

Instead, the generated SVG export is rendered at the selected target dimensions first, then converted to PNG. This keeps the SVG geometry vector-based for the high-resolution render and produces sharper edges and strokes.

The resulting filename includes the selected scale, for example:

```text
shape_centroid-full_x4.png
```

## SVG Export

SVG export creates a self-contained SVG composition with centroid metadata.

The exported SVG includes metadata describing:

- Generator/version information.
- Export mode.
- Source centroid coordinates.
- Cropped centroid pixel coordinates.
- Normalized centroid coordinates.
- Coverage bounds.
- Coverage corners.
- Output dimensions.
- Stroke settings.
- Appearance colors.

## Local-First Architecture

The project is intentionally structured as a single HTML utility.

### Core technologies

- HTML
- CSS
- Vanilla JavaScript
- SVG DOM APIs
- Canvas APIs for raster analysis / PNG encoding
- Browser File API
- Browser XML parser/serializer

There is no application backend required for normal operation.

## User Workflow

A typical SVG centroid workflow is:

1. Open the HTML file in a modern browser.
2. Go to **SVG Centroid Calculator**.
3. Import one SVG.
4. The SVG is sanitized and validated.
5. Invalid SVG files are rejected with a clear popup.
6. For valid SVGs, the application calculates bounds and centroid.
7. Adjust fill/stroke and guide appearance as needed.
8. Choose an export mode.
9. Choose PNG upscale (`x1`–`x4`) when exporting PNG.
10. Export PNG or SVG.

## Project Structure

The application is intentionally minimal:

```text
.
├── AR_centroid(1).html
├── README.md
└── .gitignore
```

The main application contains its CSS and JavaScript internally.

## Running Locally

No build step is required.

Open the HTML file directly in a modern desktop browser.

For development, it may also be served through any simple local static HTTP server.

## Browser Notes

HEIC/HEIF support depends on browser capabilities.

The application does not upload user files to an external server. SVG processing, validation, analysis, preview, and export are performed locally in the browser.

## Intended Use

This utility is useful for workflows that need:

- Fast image dimension inspection.
- SVG shape coverage measurement.
- Visual centroid detection.
- Consistent SVG appearance overrides.
- Transparent centroid PNG generation.
- High-resolution PNG output from vector SVG geometry.
- Quick SVG geometry checks before exporting assets.

## License

No license is declared by this project at this time.

If the project is published publicly, add an explicit license file such as `LICENSE` and update this section accordingly.
