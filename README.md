# The Hidden Cost of the Road
### A Data-Driven Documentary — Global Roadkill Analysis

A short documentary combining cinematic storytelling with real wildlife-roadkill data, built for CDS CIA 2. The project takes a global roadkill dataset, cleans and analyzes it in Python, extracts nine insights, and presents them as a NatGeo-style narrated documentary — opening on a European Roe Deer and closing on a conservation warning.
Please find the video attached and the original dataset attached within this google drive. Could not upload it individually on classroom due to its huge size.
https://drive.google.com/drive/folders/1PrzGqLisLnP_9mj5aB5QFuxeJP5YPO1H?usp=drive_link

---

## Table of Contents
- [Datasets](#datasets)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Data Cleaning](#data-cleaning)
- [The Nine Insights](#the-nine-insights)
- [Documentary Production](#documentary-production)
- [Methodological Notes](#methodological-notes)
- [Reproducing the Analysis](#reproducing-the-analysis)
- [Sources & Citations](#sources--citations)

---

## Datasets

### Primary: Global Roadkill Data
- **Authors:** Grilo et al., 2024/2025
- **Source:** Figshare (DOI: 10.6084/m9.figshare.25714233) and GBIF (Systematic + Opportunistic records)
- **Scope:** 177,428 cleaned records · 54 countries · 42 years (1983–2024) · 2,283 species
- **Format:** Single flat CSV, Darwin Core-style fields (species, coordinates, date, country, IUCN status, road context

---

## Tech Stack

| Tool | Used for |
|---|---|
| **Python (pandas)** | Data loading, cleaning, aggregation for all 9 insights |
| **Plotly** (`plotly.express`, `plotly.graph_objects`) | All static charts — bar charts, seasonal charts, small-multiple continent breakdown |
| **Kaleido** | Rendering Plotly figures to static PNG for video/report use (`fig.write_image()`) |
| **Kepler.gl** | Interactive hexbin hotspot maps (Insights 5 and 9) — weighted by `numberOfRoadkill`, exported as images for the final video |
| **Google Colab** | Notebook environment for the full analysis |
| **Git / GitHub** | Version control and submission |
| **DaVinci Resolve / CapCut** | Video editing — narration, chart overlays, talking-head segments |

---

## Repository Structure

```
roadkill-documentary/
├── data/
│   ├── raw/
│   │   ├── global_roadkill.csv              # Grilo et al. dataset (linked, not committed — see below)
│   │  
│   └── processed/
│       └── global_roadkill_clean.csv        # cleaned output of the notebook
├── notebooks/
│   └── roadkill_final_colab.ipynb           # full cleaning + 9-insight analysis (outputs cleared before commit)
├── assets/
│   └── charts/                              # exported PNGs for every insight, used in the video
└── README.md
```

**Note on the raw data file:** the global dataset is ~90MB, over GitHub's comfortable size limit. It is **not committed directly** — instead, `data/raw/README.md` links to the Figshare DOI and GBIF pages so anyone can re-download the original source. Only the cleaned, smaller `global_roadkill_clean.csv` (~35MB) is committed.

---

## Data Cleaning

Performed in the notebook's setup cell, on the global dataset:

- Reduced to 19 relevant columns (dropped survey-only metadata fields that are 50%+ empty by design — present only for systematic-survey records, not opportunistic sightings)
- Dropped rows missing `scientificName` or coordinates (core fields needed for every insight)
- Filled `numberOfRoadkill` nulls with 1 (a record with no count listed means one animal)
- Filled `vernacularName` nulls with the scientific name as fallback
- Filled `iucnStatus` nulls with `'NE'` (Not Evaluated — a real IUCN category)
- Removed duplicate records by `occurrenceID`
- **Did not** blanket-impute structurally-missing fields like `roadLength` with the column median — those are only meaningful for the ~45% of records that came from systematic surveys, and imputing them would fabricate data for the rest

Result: 177,428 rows retained (no records lost to the core fields), reduced from 90.5MB to ~35MB.

---

## The Nine Insights

The documentary follows a deliberate arc: **Scale → Victims → Time → World → Risk → Vulnerable Victims → Final Warning.**

| # | Insight | Headline Finding |
|---|---|---|
| 1 | What kinds of animals are being hit? | **57.9%** of all recorded roadkill are mammals — nearly 2.5x the average across all four classes |
| 2 | Who are the most common victims? | **European Roe Deer** — 44,565 deaths, ~3x the next closest species |
| 3 | When does roadkill peak? | **August** — 2.24x February's total; pattern holds across the 42-year span |
| 4 | Every continent has its victims | 6 continents, 6 different leading species — no single global "victim" |
| 5 | Where is recorded roadkill concentrated? | Hotspots align with regions running active road-ecology monitoring (Europe, Brazil, North America) |
| 6 | Where do threatened species make up the largest share? | **Asia (6.47%)** and South America (4.91%) — highest relative to their own record counts |
| 7 | How much roadkill involves threatened species? | **2.51%** (5,242 animals) — strictly Vulnerable + Endangered + Critically Endangered |
| 8 | Which threatened species are recorded most? | **Common Fire Salamander** (1,565, VU), followed by Giant Anteater (1,237, VU) |
| 9 | Where are threatened-species hotspots? | Same geographic lens as Insight 5, filtered to VU+EN+CR only |

*(Full numbers and chart-by-chart breakdown in `docs/insights.md`.)*

### Local case study — Western Ghats, India
- Dominated by amphibians and invertebrates (frogs, toads, snails, millipedes) — mammals are only 5.5% of individuals, a sharp contrast to the global dataset
- **Season effect:** 26.4 kills per 10km surveyed in monsoon vs. 11.8 in summer, even after adjusting for survey effort — more than double
- **Habitat effect:** raw kills highest on tea-plantation-bordered and forest roads

---

## Documentary Production

- **Cold open:** European Roe Deer footage with AI-narrated, documentary-style voiceover (cut to black before impact — implied, not shown)
- **Insight segments:** talking-head explainer clips (picture-in-picture over themed background footage) for Insights 1–3, presented by group members, satisfying the "every member participates" requirement
- **Chart integration:** every Plotly chart exported at 1600×900 (16:9) via Kaleido, given a slow Ken Burns zoom in editing rather than shown as a static hold
- **Narration script:** full text in `docs/script.md`, written for TTS/AI voice — numbers spelled out as words, pauses marked with ellipses, no bracketed stage directions in the spoken text itself

---

## Methodological Notes

These constraints were followed throughout the analysis and narration to keep every claim defensible:

- **Recorded, not universal** — every figure describes *recorded* roadkill in this dataset, not a complete census of global wildlife deaths
- **Strict "threatened" definition** — Vulnerable + Endangered + Critically Endangered (VU+EN+CR) only; Near Threatened (NT) is intentionally excluded throughout Insights 6–9
- **Hotspots show concentration, not verdict** — geographic maps reflect where recorded roadkill is concentrated, closely tied to where monitoring is active, not necessarily where danger is highest
- **Patterns, not proof of cause** — the seasonal pattern (Insight 3) describes what the recorded dataset shows; it does not by itself establish why
- **Row counts vs. animal counts** — every aggregation sums `numberOfRoadkill` (actual animals per incident), not raw row counts, since some roadkill events involve multiple animals — a naive row-count approach undercounts amphibians by ~44%

---

## Reproducing the Analysis

```bash
# clone the repo
git clone <repo-url>
cd roadkill-documentary



`requirements.txt`:
```
pandas
plotly
kaleido
keplergl
numpy
```

Open `notebooks/roadkill_final_colab.ipynb` in Jupyter or Colab, mount/point to the raw data (see `data/raw/README.md` for download links), and run cells top to bottom. Kepler.gl map cells require manual layer configuration (Hexbin, weighted by `numberOfRoadkill`) as noted in the notebook's markdown cells.

---

## Sources & Citations

- Grilo, C. et al. (2024/2025). *Global Roadkill Data.* Figshare. https://doi.org/10.6084/m9.figshare.25714233
- Jeganathan, P., Mudappa, D., Kumar, M. A., & Raman, T. R. S. (2018). Seasonal variation in wildlife roadkills in plantations and tropical rainforest in the Anamalai Hills, Western Ghats, India. *Current Science*, 114(3), 619–626. https://doi.org/10.18520/cs/v114/i03/619-626
