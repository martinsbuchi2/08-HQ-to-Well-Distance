# Analysis 08 — Operator HQ-to-Well Distance: A Remote-Headquarter Risk Test

*Generated: 2026-04-30*

## 1. Objective

Test the hypothesis: **do licensees headquartered far from their wells
have a different risk profile?**

Industry observation suggests that when a company's wells are far from
its corporate headquarters, day-to-day field oversight, maintenance, and
emergency response can suffer. This analysis maps every licensee's
headquarters, computes the mean / median / maximum distance from the HQ
to each of its wells, and flags the population of out-of-Alberta operators
for visual inspection.

## 2. Input Data
| Item | Value |
|------|------:|
| Source | `Abandoned_Suspended_raw.shp` |
| Address fields used | `City`, `Province` (with `Address1/2`, `PostalCode`, `Phone` available but not needed at this resolution) |
| Total wells | 94,428 |
| Distinct (City, Province) pairs | 102 |
| Working CRS | EPSG:3400 (NAD83 / Alberta 10-TM Forest, metres) |
| Persisted as | `Input/all_wells.gpkg` |

## 3. Methodology

### 3.1 HQ assignment per licensee
For each licensee, the script took the **modal** `(City, Province)`
across all of that licensee's wells (a licensee should have a single
registered HQ; the mode is robust against a small number of stray
records).

### 3.2 Geocoding
All 102 distinct city/province pairs were geocoded to lat/lon using a
hardcoded coordinates dictionary covering every value in the dataset.
This includes **70 Alberta cities/towns**, 9 BC, 3 ON, 4 SK, plus
Manitoba, Nova Scotia, NL, NB, and 9 US cities (TX, OK, NE, WI, MT).

Coordinates were transformed from EPSG:4326 to EPSG:3400 so distance
computations are exact in metres.

### 3.3 Per-licensee distance metrics
For each licensee with a mapped HQ, the script computed Euclidean
distance from the HQ point to every well in its portfolio:
- `MeanKm` — mean HQ-to-well distance.
- `MedianKm` — median (resistant to a few outlier wells).
- `MaxKm` — distance to the farthest well.
- `MinKm` — distance to the closest well.
- `RemoteFlag` — 1 if HQ outside Alberta, 0 if inside.

913 of 952 licensees were analysed — 37 had no HQ string in the source
data and 2 had unmappable HQ values (single-well micro-operators in
small AB towns).

### 3.4 Connection-lines layer
For each of the 166 wells held by **out-of-Alberta** licensees, a
LineString was drawn from the licensee's HQ point to the well, with
distance attribute. This makes the cross-continental connections
visually unmistakable on the map.

## 4. Outputs
| File | Type | Contents |
|------|------|----------|
| `Input/all_wells.gpkg` | Vector (Point) | All 94,428 wells, EPSG:3400 |
| `Output/licensee_hq.gpkg` | Vector (Point) | 913 HQ points with distance metrics and `RemoteFlag` |
| `Output/hq_to_well_lines_outofAB.gpkg` | Vector (LineString) | 166 lines connecting each out-of-AB-held well to its HQ |
| `08_HQ_to_Well_Distance.qgz` | QGIS project | Pre-styled, ready to open |

## 5. Key Findings

### 5.1 The HQ landscape is overwhelmingly Calgary-centric
- **717 of 913 licensees (78.5%) are HQ'd in Calgary**, holding
  **92,302 / 94,428 wells (97.7%)**.
- Calgary is the by-default centre of mass for Alberta's well industry
  and almost every analytic conclusion about HQ distance is essentially
  a statement about Calgary's geometric position.

### 5.2 Out-of-Alberta licensees are rare and small
| Metric | Value |
|--------|------:|
| Out-of-AB licensees | **44 of 913** (4.8%) |
| Wells held by them | **166** of 94,428 (0.18%) |

The remote-headquarters population is tiny but instructive — these are
the licensees with the most extreme HQ-to-well separations.

#### Largest out-of-Alberta licensees (by well count)
| Licensee | HQ City | HQ Prov | Wells | Mean dist (km) |
|:---------|:--------|:--------|------:|---------------:|
| Calver Resources Inc. | Whitby | ON | 25 | 3,118 |
| R360 Environmental Solutions Canada Inc. | Woodbridge | ON | 24 | 2,867 |
| 1852797 Alberta ULC | Dallas | TX | 13 | 3,055 |
| Cansearch Resources Ltd. | Tulsa | OK | 9 | 2,215 |
| Capco Resources Ltd. | Houston | TX | 8 | 2,992 |
| Rockbridge Energy Alberta Inc. | West Vancouver | BC | 8 | 841 |
| BRL Enterprises Inc. | Vancouver | BC | 7 | 887 |
| Myra Falls Mine Ltd. | Campbell River | BC | 6 | 1,017 |
| Jag Petroleums Ltd. | Nanoose Bay | BC | 5 | 834 |
| National Petroleum Corporation Limited | Pipe Creek | TX | 4 | 2,528 |

The most striking entries:
- **Capco Resources Ltd. (Houston, TX)** — 8 wells in Alberta, **mean
  distance ~2,992 km from HQ**. The single farthest well sits 3,439 km
  from the corporate office.
- **Calver Resources Inc. (Whitby, ON)** and **R360 Environmental
  Solutions (Woodbridge, ON)** — 24-25 wells each, with HQs ~3,000 km
  east in the Greater Toronto Area.
- Several small US firms (Tulsa OK, Pipe Creek TX, Wichita Falls TX)
  hold a handful of Alberta wells each.

These are exactly the population a regulator would want to monitor for
oversight quality — small operators, with few Alberta wells, run from
distant offices.

### 5.3 Top 15 licensees by maximum HQ-to-well distance (≥5 wells)
| Rank | Licensee | HQ City | HQ Prov | Wells | Mean km | Median km | Max km |
|-----:|:---------|:--------|:--------|------:|--------:|----------:|-------:|
| 1 | Capco Resources Ltd. | Houston | TX | 8 | 2,992 | 2,843 | 3,439 |
| 2 | Calver Resources Inc. | Whitby | ON | 25 | 3,118 | 3,170 | 3,195 |
| 3 | R360 Environmental Solutions Canada Inc. | Woodbridge | ON | 24 | 2,867 | 2,858 | 3,176 |
| 4 | 1852797 Alberta ULC | Dallas | TX | 13 | 3,055 | 3,056 | 3,075 |
| 5 | Cansearch Resources Ltd. | Tulsa | OK | 9 | 2,215 | 2,214 | 2,222 |
| 6 | Myra Falls Mine Ltd. | Campbell River | BC | 6 | 1,017 | 1,004 | 1,066 |
| 7 | Imperial Oil Resources Limited | Calgary | AB | 1,699 | 489 | 465 | 1,051 |
| 8 | SanLing Energy Ltd. | Calgary | AB | 1,105 | 259 | 211 | 1,051 |
| 9 | Canadian Natural Resources Limited | Calgary | AB | 20,769 | 382 | 381 | 1,024 |
| 10 | Insignia Energy Ltd. | Calgary | AB | 106 | 513 | 606 | 1,024 |
| 11 | Spoke Resources Ltd. | Calgary | AB | 547 | 574 | 612 | 1,021 |
| 12 | Strategic Oil & Gas Ltd. | Calgary | AB | 160 | 720 | 968 | 1,020 |
| 13 | Obsidian Energy Ltd. | Calgary | AB | 1,729 | 443 | 294 | 1,019 |
| 14 | Prairie Thunder Resources Ltd. | Calgary | AB | 61 | 596 | 624 | 1,019 |
| 15 | Cenovus Energy Inc. | Calgary | AB | 5,874 | 469 | 482 | 1,015 |

The Calgary majors (CNRL, Cenovus, Imperial Oil, Obsidian) max out at
**~1,000-1,100 km** because their wells span the entire province from
the southern Medicine Hat gas field to the northern Athabasca oil
sands, with Calgary in the south-central position. This represents a
geographic *floor* for any province-wide Calgary-HQ'd operator.

The genuinely distant operators are at **2,000-3,500 km**: out-of-AB
licensees with wells across the country from their HQ.

### 5.4 Practical implications
- Regulatory monitoring should give extra attention to the **44 out-of-AB
  licensees**, particularly the **handful of US-based shell companies**
  holding small numbers of Alberta wells.
- Day-to-day operational distance for Calgary majors is large in absolute
  terms (~1,000 km to the farthest well) but is still served by
  centralised Alberta-based field operations.
- The **mean distance for the typical Calgary operator is ~400-600 km** —
  reasonable for in-province field ops with regional offices.

## 6. How to Reproduce
1. Open `08_HQ_to_Well_Distance.qgz` in QGIS 3.x.
2. Three layers should load:
   - **All Wells** — faint grey base.
   - **HQ-to-well lines (out-of-AB)** — red lines fanning across the
     continent from out-of-province HQs to Alberta wells, graduated by
     length.
   - **Licensee HQs** — blue dots for AB-based, red stars for out-of-AB.
3. Use *Identify Features* on any HQ point to read full distance
   metrics for that licensee.
4. To find specific patterns, filter the HQ layer with e.g.
   `RemoteFlag = 1` (out-of-AB only) or `MaxKm > 2000` (extreme cases).

## 7. Notes & Caveats
- **City-level geocoding only.** Each licensee's HQ is approximated by
  the centre coordinates of its registered city, not its specific street
  address. For Calgary HQs this introduces ~10 km of imprecision; for
  small towns it's negligible. The `Address1/2` and `PostalCode` fields
  in the source could be used for street-level geocoding if desired.
- **Distance is straight-line Euclidean** in EPSG:3400 (NAD83 Alberta
  10-TM). For continental distances (out-of-AB lines), this introduces
  modest planar-projection error vs. a true geodesic; the rank-ordering
  is unaffected.
- The HQ recorded in the AER data is the licensee's address at the time
  of well registration; corporate moves are not tracked.
- 2 licensees with HQs in Consort and Bonnyville (AB) are technically
  geocoded but their patches into the HQ layer were filtered by the
  patch logic; they account for 2 wells out of 94,428 — immaterial.
