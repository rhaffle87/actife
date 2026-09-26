# LORAN Removal Plan & Inventory Audit for ACTIFE

## 1. Context & Objective
The Loran-C, eLoran, and RF Waveform simulation features have been extracted and completely modernized in the standalone repository **LORAN LAB** (`e:/Projects/lmao/eloran/`, GitHub: `https://github.com/rhaffle87/eloran`).

This document details the inventory and removal plan for decoupling all radio-navigation code from **ACTIFE** (`e:/Projects/lmao/actife`), returning ACTIFE to a focused toolkit strictly dedicated to AI/ML, computer vision, image processing, signal processing, and color science.

---

## 2. Comprehensive Inventory & Classification

Across the entire ACTIFE repository (excluding `node_modules`, `dist`, `.git`), we performed exhaustive case-insensitive regex searches for:
`loran`, `eloran`, `loranc`, `waveforms`, `asf`, `tdoa`, `gdop`, `gri`, `hyperbol`, `marching`, `lop`, `gridWorker`, `asfWorker`, `simplify`, `stations_example`, `stations_`, `maplibre`, `proj4`, `turf`, `papaparse`, `"100 kHz"`.

### Category A: Files to REMOVE (Loran-Only)
These files are used exclusively by Loran modules and have zero imports from remaining AI/ML or signal modules:

1. `src/components/Eloran.jsx` (REMOVE)
   - *Proof:* eLoran simulator component. Not imported by any remaining module.
2. `src/components/Loranc.jsx` (REMOVE)
   - *Proof:* Loran-C simulator component. Not imported by any remaining module.
3. `src/components/Waveforms.jsx` (REMOVE)
   - *Proof:* 100 kHz pulse oscilloscope component. Only imported by `Eloran.jsx` and `Loranc.jsx`.
4. `src/components/Waveforms.css` (REMOVE)
   - *Proof:* Stylesheet for `Waveforms.jsx`.
5. `src/workers/gridWorker.js` (REMOVE)
   - *Proof:* Multi-station TDOA marching-squares worker. Only instantiated by `Eloran.jsx` and `Loranc.jsx`.
6. `src/workers/asfWorker.js` (REMOVE)
   - *Proof:* ASF formula evaluation worker. Only instantiated by `Eloran.jsx`.
7. `src/utils/simplify.js` (REMOVE)
   - *Proof:* Ramer–Douglas–Peucker algorithm for LOP contour simplification. Only imported by `Eloran.jsx`.
8. `src/examples/asf_example.js` (REMOVE)
   - *Proof:* Example ASF formula script. Unreferenced orphan.
9. `public/examples/stations_example.csv` (REMOVE)
   - *Proof:* Jakarta sample station coordinates. Only fetched by `Eloran.jsx` and `Loranc.jsx`.

### Category B: Files to KEEP and CLEAN (SHARED)
These files contain non-Loran features, but have routing, links, or text references to Loran:

1. `src/App.jsx` (SHARED - EDIT)
   - Remove lazy imports: `Loranc`, `ELoranSimulator`
   - Remove route definitions: `/loran-c`, `/eloran`
   - Remove `getActiveSection` cases: `'/loran-c'`, `'/eloran'`
2. `src/components/Navbar.jsx` (SHARED - EDIT)
   - Remove `features` entries: `'loran-c'` (LORAN-C Simulator), `'eloran'` (eLORAN Simulator)
   - Remove unused `MapPin` icon import
3. `src/components/Tutorials.jsx` (SHARED - EDIT)
   - Remove tutorial card: `{ id: 'loran-c', title: 'LORAN-C Simulator', ... }`
4. `src/components/Home.jsx` (SHARED - EDIT)
   - Add a discrete notice: *"Loran-C and eLoran simulators have moved to [LORAN LAB](https://eloran-one.vercel.app)."*
5. `sitemap.xml` (SHARED - EDIT)
   - Remove `<url><loc>https://actife.vercel.app/loran-c</loc>...</url>`
6. `README.md` (SHARED - EDIT)
   - Remove Loran-C sections, features, and directory references
   - Add single notice linking to `https://eloran-one.vercel.app`
7. `vercel.json` (SHARED - EDIT)
   - Add permanent (301) redirects for `/eloran`, `/loran-c`, `/loranc`, `/waveforms` to `https://eloran-one.vercel.app` BEFORE the catch-all rewrite.
8. `vite.config.js` (SHARED - EDIT)
   - Remove `maplibre: ['maplibre-gl']` from `manualChunks`.
9. `package.json` (SHARED - EDIT)
   - Uninstall unused dependencies via `npm uninstall`.

### Category C: Unused / Special Files (UNSURE / AUDIT)
1. `api/search.js` (Google Custom Search proxy):
   - *Audit:* Not imported by any component in `src/`. Contains Google Custom Search API proxy logic. Per explicit prompt instruction: **Keep it and report to user, do not delete.**
2. Images in `src/assets/`:
   - `logo.png`, `logo1.png`, `hero-img.png`, `favicon.ico`, `react.svg` are all used by `Home.jsx`, `Credits.jsx`, `index.html`. None are Loran-only. Keep all.
3. Other workers in `src/workers/`:
   - `src/workers/imageWorker.js`: Used by `src/components/ImageProcessing.jsx`. MUST BE KEPT.

---

## 3. Dependency Audit

| Dependency | Imported By | Action | Rationale |
| :--- | :--- | :--- | :--- |
| `maplibre-gl` | `Eloran.jsx`, `Loranc.jsx` | **UNINSTALL** | Only used for Loran map rendering |
| `proj4` | `Eloran.jsx`, `Loranc.jsx`, `gridWorker.js` | **UNINSTALL** | Only used for Loran coordinate projections |
| `papaparse` | `Eloran.jsx`, `Loranc.jsx` | **UNINSTALL** | Only used for Loran CSV station import/export |
| `@turf/turf` | None (was in package.json) | **UNINSTALL** | Zero usages across entire ACTIFE repository |
| `chart.js` | `ColorScience.jsx`, `NeuralNetwork.jsx`, `SignalProcessing.jsx` | **KEEP** | Essential for AI/ML loss plots and color spaces |
| `react-chartjs-2` | `ColorScience.jsx`, `NeuralNetwork.jsx`, `SignalProcessing.jsx` | **KEEP** | React wrapper for Chart.js in remaining modules |
| `mathjs`, `plotly.js`, `@tensorflow/tfjs`, `d3-*` | Various AI/ML modules | **KEEP** | Core AI/ML dependencies |

---

## 4. Execution Step Sequence

1. **Commit 1: Routes & UI**
   - Update `src/App.jsx`, `src/components/Navbar.jsx`, `src/components/Tutorials.jsx`, `src/components/Home.jsx`.
2. **Commit 2: Delete Loran-Only Components**
   - Delete `Eloran.jsx`, `Loranc.jsx`, `Waveforms.jsx`, `Waveforms.css`.
3. **Commit 3: Delete Loran-Only Workers & Utilities**
   - Delete `gridWorker.js`, `asfWorker.js`, `simplify.js`, `asf_example.js`.
4. **Commit 4: Delete Loran-Only Assets & Clean Sitemap**
   - Delete `public/examples/stations_example.csv` (and empty directory if appropriate).
   - Clean `sitemap.xml`.
5. **Commit 5: Uninstall Dependencies & Clean Vite Config**
   - Run `npm uninstall maplibre-gl proj4 papaparse @turf/turf`.
   - Remove `maplibre` chunk from `vite.config.js`.
6. **Commit 6: Configuration, Redirects & Documentation**
   - Update `vercel.json` with 301 redirects to LORAN LAB.
   - Update `README.md`.
7. **Commit 7: Verification**
   - Run `npm run lint` and `npm run build`.
   - Grep verification.
