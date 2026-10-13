# Who Owns Downtown Temple City

An ownership-cluster map of 823 parcels in downtown Temple City, CA, built with the same methodology as the "Who Owns Downtown El Paso" Knokol Map.

Open `index.html` in a browser (or publish it to GitHub Pages) — no build step, no server required.

## What it shows

- **823 parcels**, drawn to their real assessor-roll footprints wherever a match was available (819 of 823).
- **57 ownership networks** of 2+ parcels, worth **$112.5M** in assessed value combined, identified by linking records that share a mailing address, an owner/principal name, or a close fuzzy-matched name.
- The **12 largest networks** get their own color on the map and in the sidebar; the other 45 clustered networks are shown in gray. Single, unclustered parcels are shown dim.
- Each network is labeled **confirmed** (a human verified the beneficial owner via OpenCorporates or a Statement of Information) or **inferred** (the link rests on data-matching alone).
- Dashed "thread" lines connect each network's centroid to its scattered parcels.
- Toggle layers: basemap (plain / aerial), street labels, **building footprints**, and **zoning**.

## Methodology

### Clustering (union-find over three link types)

Starting from the parcel-level assessor roll, parcels are linked into the same ownership network when any of the following hold:

1. **Shared mailing address** — normalized owner mailing address (house number, street, unit, city) matches across parcels.
2. **Matching owner/principal name** — normalized owner name, secondary owner name, or "care of" name matches exactly after stripping common trust boilerplate (TRUST, TRUSTEE, REVOCABLE, LIVING, FAMILY, co-owner joiners, etc.).
3. **Fuzzy name match** — distinct owner names that are a ≥90% token-sort match (handles minor punctuation/ordering differences and typos in the source roll) and differ by no more than 12 characters in length.

Using entity name and mailing address together (rather than mailing address alone) recovers links that address-only matching misses — for example, an LLC whose own assessor-record mailing address differs from the rest of its network's shared address, but whose registered manager ties it back to the same principal.

A keyword-based classifier also assigns each parcel an **owner type** (LLC/Corp, Government/Public, Religious/Nonprofit, Individual Trust, Individual), which in turn feeds a **holding structure** label for each network (e.g., "Multi-parcel LLC holding," "Family trust portfolio," "Landlord–tenant / affiliated entity pair," "Single-purpose LLC chain").

### Confirmed vs. inferred

Every clustered network starts out **inferred**. Where the underlying LLC or trust could be tied to a named individual — by manually searching [OpenCorporates](https://opencorporates.com) and, where needed, California Statement of Information filings — the network is marked **confirmed** and the principal's name is shown in the tooltip and sidebar.

California's Secretary of State does not publish a free bulk or API feed of business-entity data (only single-entity lookups through bizfileOnline, paid certified copies, or a Public Records Act request for bulk extracts), so this research was done entity-by-entity and is captured in `Temple_City_Ownership_Clusters.xlsx` (OpenCorporates Queue / Additional Leads tabs) alongside this map.

### Parcel & building footprints

Real footprints are pulled from LA County's public GIS (no auth required):

- **Parcels** — LA County Assessor's `CEO_PARCEL_SPATIAL_INFO` ArcGIS REST service, queried by AIN.
- **Buildings** — LA County DPW's `buildingfootprints` ArcGIS REST service, queried by bounding box.

819 of 823 parcels matched a footprint by AIN; the remaining 4 are drawn as point markers at their assessor-roll coordinates. 3,004 building footprints are shown as a dim gray context layer.

### Zoning layer

The zoning layer uses the `zoning` field already present on the parcel roll, mapped to eight categories (R3 and R-3, which both mean "High Density Residential," are merged into one color — the roll has inconsistent formatting for the same zone):

| Code | Zone |
|---|---|
| R1 | Low Density Residential |
| R2 | Medium Density Residential |
| R3 / R-3 | High Density Residential |
| MU-M | Mixed Use Medium |
| DC | Downtown Commercial |
| LTC | Las Tunas Commercial |
| OS | Open Space |
| C-G | General Commercial Zone |

It's off by default and only draws on the 819 parcels with a matched footprint polygon.

### Sanborn maps

Temple City was incorporated in 1946, after the main Library of Congress Sanborn Map survey era — a direct search of the LOC's Sanborn collection for Temple City returned zero results, so no historical fire-insurance-map layer is included (unlike the El Paso build, which had 1908 coverage).

## Files

- `index.html` — the standalone map (self-contained; embeds its own data, no external data files needed beyond CDN-hosted Leaflet and Esri basemap tiles).
- `Temple_City_Ownership_Clusters.xlsx` — Cluster Summary (with holding-structure classification), Parcel Detail, OpenCorporates Queue, and Additional Leads tabs documenting the manual research behind the confirmed/inferred calls.
- `temple-city.csv` — the source parcel roll (not modified).

## Limitations

- Clustering is based on name and address matching against a single county assessor snapshot; it will miss ownership ties that don't show up in either signal (e.g., informal agreements, unrecorded trusts) and can occasionally over-link common names.
- "Confirmed" status reflects the most recent manual OpenCorporates/Statement-of-Information lookup; corporate filings change over time.
- 4 of 823 parcels have no matched building/parcel outline and are shown as point markers only.
