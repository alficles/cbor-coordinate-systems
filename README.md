# CBOR Coordinate Reference System (CRS) Tag

Working area for **`draft-lemmons-cbor-coordinate-systems`**, a continuation of the
expired individual draft **`draft-clarke-cbor-crs`**, which defines a Concise Binary
Object Representation (CBOR) tag (**tag 104**) for associating a **Coordinate
Reference System (CRS)** with geographic coordinates (companion to the Geographic
Coordinates CBOR tag 103).

The goal of this effort is to progress that document and to **extend it to support
coordinate systems on other celestial bodies**, not just Earth.

## Repository layout

| Path | What it is |
|------|------------|
| `coordinate-systems.md` | **The source.** mmark document for `draft-lemmons-cbor-coordinate-systems`. Currently a faithful, complete conversion of Clarke's `-02` (no technical changes yet). |
| `Makefile` | Build via `mmark` + `xml2rfc` into `gen/`. |
| `gen/` | Build output (`.xml`/`.txt`/`.html`, git-ignored). |
| `draft-clarke-cbor-crs-02.xml` | Pristine original source, retained for provenance/diffing. |

## Building

Requires [`mmark`](https://github.com/mmarkdown/mmark) and
[`xml2rfc`](https://pypi.org/project/xml2rfc/) on `PATH`.

```
make            # -> gen/draft-lemmons-cbor-coordinate-systems-00.{xml,txt,html}
make clean
```

Bump `VERSION` in the `Makefile` at each submission.

## Provenance of the conversion

`coordinate-systems.md` is a faithful conversion of the existing draft:

- Original: `draft-clarke-cbor-crs-02`, by Trevor R.H. Clarke (Ball Aerospace &
  Technologies Corp.), Informational, expired 2020-09-18 (individual, never
  WG-adopted). Source: <https://www.ietf.org/archive/id/draft-clarke-cbor-crs-02.xml>
- The `-02` was xml2rfc v2 (kramdown-rfc2629). This repo re-authors it in **mmark**
  as a fresh Lemmons individual draft.
- The conversion is **verbatim**: body text is reproduced exactly as written,
  including the original typos and the original reference labels. The only deviations
  from `-02` are non-editorial -- the draft identity (renamed to
  `draft-lemmons-cbor-coordinate-systems-00`, re-authored by Chris Lemmons) and the
  source format. Corrections are deferred to later commits (see Next steps).

## Existing semantics (MUST be preserved -- deployments exist)

Tag 104 dispatches on the **CBOR content type** of its data item:

| Content type        | Meaning                          | Status               |
|---------------------|----------------------------------|----------------------|
| unsigned int (MT0)  | EPSG SRID (e.g. `104(4326)`)     | frozen (normative)   |
| text string (MT3)   | OGC Well-Known Text (WKT)         | frozen               |

## Planned direction for celestial support

Dispatch is by CBOR content type, so planetary support is added **without touching
either existing branch** -- an old decoder can never silently misread a new value;
worst case is a clean unknown-type error.

1. **Clarify the WKT branch as WKT2** ([ISO 19162:2019] / OGC 18-010). WKT2 is
   body-agnostic, so a Mars/Moon CRS is already expressible as a WKT2 string.
2. **Add a new array-typed, authority-qualified identifier** (MT4) as the general
   SRID mechanism, with EPSG as the default-authority shorthand:
   - `104(4326)` == `104(["EPSG", 4326])`
   - `104(["IAU_2015", 49900])`  -- Mars (ocentric)
   - `104(["IAU_2015", 19900])`  -- Mercury
   (Do **not** overload the WKT string branch with an OGC URN, and do **not** carve up
   the EPSG integer space.)

## Key references (verified)

- **IAU body coordinate systems (normative science):** Archinal, B. A., et al.,
  "Report of the IAU Working Group on Cartographic Coordinates and Rotational
  Elements: 2015", *Celestial Mechanics and Dynamical Astronomy* **130**(3), art. 22,
  Feb 2018, DOI **10.1007/s10569-017-9805-5**.
  - Correction: *ibid.* **131**(12), art. 61, Dec 2019, DOI
    **10.1007/s10569-019-9925-1**.
- **CRS encoding / model:** WKT2 -- ISO 19162:2019 (OGC 18-010); CRS abstract model
  -- ISO 19111:2019 (OGC Abstract Specification Topic 2).
- **Planetary CRS code namespace (informative):** USGS / Observatoire de Paris VESPA
  registry <https://voparis-vespa-crs.obspm.fr/> ; implemented as the `IAU_2015`
  authority in PROJ >= 8.1 / GDAL. (De-facto today; on a path to OGC standardization.)
- **CBOR base:** the conversion faithfully retains RFC 7049; updating to RFC 8949 is a
  planned edit.
- **Companion tag 103** ("Geographic Coordinates") is currently an AllThingsTalk
  community spec, not an RFC -- this soft dependency needs firming up before advancing.

## Next steps (editorial + technical)

- Correct the typos preserved verbatim from the original: "standarized" ->
  "standardized"; "complete CRS specifcation of and subtype of CRS" -> "complete CRS
  specification of any subtype of CRS"; "provice" -> "provide"; "Pertroleum" ->
  "Petroleum"; "the tag introduced here are not expected" -> "... is not expected".
- Fold in the celestial support above (WKT2 reference; MT4 authority-qualified array).
- Update the CBOR base reference RFC 7049 -> RFC 8949.
- Firm up (or absorb) the tag 103 dependency.
- Drop the tutorial-artifact sentence "You will learn this further below." carried over
  verbatim from the original Introduction.
- Add a Terminology/BCP 14 section and capitalize requirement keywords if the document
  is progressed toward Standards Track.
