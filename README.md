# South Australia's historical shellfish ecosystems

An interactive map of 142 recorded occurrences of *Ostrea angasi*, *Pinna bicolor* and
*Malleus meridianus* ecosystems across Spencer Gulf, Gulf St Vincent and the South
Australian west coast — the assemblages that, by the mid-twentieth century, had almost
entirely gone.

**Live map:** https://bmarruda03.github.io/REPO-NAME/

*(Replace `REPO-NAME` above once GitHub Pages is enabled.)*

Produced in support of the essay *South Australia, the Oyster State*.

---

## Data source

Every mapped point is reproduced from the supplementary dataset (`mmc2`) and Figure 3 of:

> Martin, B., Huveneers, C., Reeves, S. & Baring, R. (2025). Reviving shellfish reef
> socio-ecological histories for modern management and restoration.
> *Ocean & Coastal Management*. Available online 13 January 2025;
> PII S0964-5691(25)00002-X.

That paper is open access under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The data are reused here with
attribution, as the licence requires. **The attribution and licence statement in the map's
sources panel must not be removed.**

Historical, legislative and national context presented alongside the data:

> Alleway, H.K. & Connell, S.D. (2015). Loss of an ecological baseline through the
> eradication of oyster reefs from coastal ecosystems and human memory.
> *Conservation Biology* 29(3): 795–804. doi:10.1111/cobi.12452

> Gillies, C.L., McLeod, I.M., Alleway, H.K., Cook, P., Crawford, C., Creighton, C.,
> Diggles, B., Ford, J., Hamer, P., Heller-Wagner, G., Lebrault, E., Le Port, A.,
> Russell, K., Sheaves, M. & Warnock, B. (2018). Australian shellfish ecosystems:
> Past distribution, current status and future direction. *PLOS ONE* 13(2): e0190914.
> doi:10.1371/journal.pone.0190914

### Outstanding

- The Martin et al. (2025) citation still needs its **volume and article number**, marked
  in grey in the map's sources panel. Edit the `SOURCES` object in `index.html`.
- The **67 → 0** figure sometimes attributed to Gillies et al. (2018) has not been traced
  to a specific figure or table in that paper. Verify before citing it, or restate it as a
  derivation from their dataset.

---

## Contents

```
index.html                              the map: data, styling and logic in one file
data/martin2025_sa_shellfish_sites.csv  the 142 records as a standalone dataset
vendor/leaflet/                         Leaflet 1.9.4, vendored so the map needs no CDN
.nojekyll                               tells GitHub Pages to serve files unprocessed
```

### `data/martin2025_sa_shellfish_sites.csv`

142 rows, 18 columns. Loads directly into QGIS as delimited text (`lat`/`lng`, EPSG:4326).

`sha256: 519178cf08bbd03558d8599d1f01db0ab880098b76193c046c5fcbba0c1766f0`

Both the supplied and normalised forms of each altered field are retained, so nothing from
the source is lost:

| Column | Notes |
|---|---|
| `record_id` | Matches the `mmc2 record N` reference in each map popup |
| `taxon_class_supplied` / `taxon_scientific` | Source class, and its scientific-name form |
| `site_label_supplied` / `site_label_display` | Source label, and the cleaned version |
| `region_supplied` / `region_normalised` | Source region, and SA-standard naming |
| `year`, `date_text`, `era` | Parsed from label text — see caveats |
| `record_type` | Lease/claim boundary, declared closure area, or observation |
| `confidence` | High or low, as classed in the source |
| `area_km2` | See caveats — **do not sum this column** |
| `lat`, `lng` | Unaltered from source |
| `coord_decimals`, `coord_precision_flag` | Inferred precision |

Note: `index.html` currently carries its own copy of the records inline rather than
reading the CSV at runtime. If you edit one, edit both, or the two will drift.

---

## Caveats

Also listed under *Data handling caveats* in the map itself.

- **Do not sum `area_km2`.** Three values recur across multiple records (25.719, 29.008 and
  36.001 km²), which suggests one digitised polygon assigned to several records. Most area
  values describe lease or closure boundaries, not measured reef footprints.
- **Dates are parsed from label text**, not from a dedicated date field. Era filtering is
  indicative. 141 of 142 labels contain exactly one four-digit year; one is undated.
- **Eleven records carry coordinates rounded to two decimal places** (≈1 km or coarser).
  All eleven are the 1981 survey records, which suggests coarse digitisation from a
  published figure. Each is flagged in its popup and in `coord_precision_flag`.
- **Site labels have been normalised** for capitalisation and obvious spelling
  (e.g. Hardwhicke → Hardwicke; Smokey → Smoky). No coordinate, taxon, confidence or area
  value has been altered.
- **Region assignment follows the source.** Encounter Bay, Middleton and Port Elliot
  records are binned to Gulf St Vincent although they sit outside it.

---

## Map symbology

One variable per visual channel:

| Channel | Encodes |
|---|---|
| Hue | Taxon — Okabe–Ito palette, chosen for colour-vision deficiency safety |
| Outer ring | A second taxon co-occurs in the record |
| Filled vs hollow | Confidence — filled = high, hollow = low |
| Symbol size | Whether the record contains *Ostrea angasi* — **emphasis only, not area** |

True-scale area circles are a separate toggle (*Estimated extent*), which is the only
honest way to show extent given that just 27 of 142 records carry an area value.

---

## Editing

Both live near the top of the `<script>` block in `index.html`:

- **Citations** — the `SOURCES` object is the single place they are defined.
- **Per-record provenance** — if the source dataset carries an archival reference for a
  record, add `"src": "SRSA GRG 45/1, 1885 lease register"` to that site object and the
  popup will display it above the dataset citation.

---

## Dependencies

- [Leaflet](https://leafletjs.com/) 1.9.4 — BSD-2-Clause, licence at
  `vendor/leaflet/LICENSE`
- Basemap tiles © [CARTO](https://carto.com/), map data ©
  [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors. Loaded from
  CARTO's tile service at runtime and subject to their terms of use; check current usage
  limits before promoting the map widely.

## Local preview

Opening `index.html` from the filesystem works. To serve it over HTTP as GitHub Pages
will:

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

Basemap tiles require an internet connection either way.
