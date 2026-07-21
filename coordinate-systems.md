%%%
title = "Concise Binary Object Representation (CBOR) Tag for Coordinate Reference System (CRS) Specification"
abbrev = "CBOR CRS Tag"
ipr = "trust200902"
area = "Applications and Real-Time"
workgroup = "CBOR"
submissiontype = "IETF"
keyword = ["CBOR", "CRS", "coordinate reference system", "geographic coordinates", "WKT", "EPSG"]

[seriesInfo]
name = "Internet-Draft"
value = "draft-lemmons-cbor-coordinate-systems-00"
stream = "IETF"
status = "informational"

[[author]]
initials = "C."
surname = "Lemmons"
fullname = "Chris Lemmons"
organization = "Comcast"
  [author.address]
  email = "chris_lemmons@comcast.com"
%%%

.# Abstract

The Concise Binary Object Representation (CBOR, RFC 8949) is a data format
whose design goals include the possibility of extremely small code size,
fairly small message size, and extensibility without the need for version
negotiation.

In CBOR, one point of extensibility is the definition of CBOR tags. An
existing CBOR tag, 103, allows for the representation of geographic
coordinates. Proper exploitation of geographic coordinates requires an
associated reference frame. The present document defines a CBOR tag for
referencing the coordinate reference system (CRS) for a geographic coordinate.
It is intended as the reference document for the IANA registration of the CBOR
tag defined.

{mainmatter}

# Introduction

Specifying geographic coordinates for a location on Earth requires the
definition of a coordinate reference system (CRS). A CRS is made up of several
components: [@?EDS1]

- Coordinate system: The X, Y grid upon which your data is overlayed and how
  you define where a point is located in space.

- Horizontal and vertical units: The units used to define the grid along the
  x, y (and z) axis.

- Datum: A modeled version of the shape of the Earth which defines the origin
  used to place the coordinate system in space. You will learn this further
  below.

- Projection Information: The mathematical equation used to flatten objects
  that are on a round surface (e.g. the Earth) so you can view them on a flat
  surface (e.g. your computer screens or a paper map).

# Objectives

This document aims to address the specification of coordinate reference
systems in CBOR [@!RFC8949] encoded data. This is accomplished using complete
CRS specification or through a well-known spatial reference identifier.

# Applicability

This tag (104) is designed for use with the Geographic Coordinates CBOR Tag
103 [@!CBOR-GC]. A CRS tag may specify a default CRS for an entire scope by
using it without an associated tag 103. This tag may also be encoded just
after tag 103 as a way of associating a CRS with a specific Geographic
Coordinate. This is the expected application but this tag may be used in any
relevant context.

# Semantics

The content of tag number 104 is a single CBOR data item, the type of which
selects the method used to specify the CRS. This document defines two
permitted content types, described in the following subsections:

- a text string (major type 3), interpreted as Well-Known Text;
- an unsigned integer (major type 0), interpreted as an EPSG SRID.

Content of any other type is not defined by this document.

## Well-known Text

OGC [@?OGC] Well-known Text (WKT) [@!WKT] is a standarized format for CRS
specification. When the tag content is a text string (major type 3), it is
interpreted as OGC WKT. This allows for complete
CRS specifcation of and subtype of CRS.

## EPSG Spatial Reference Identifier

A spatial reference identifier (SRID) is a code assigned by an authority
to a CRS. The same code can denote different CRSs under different
authorities. Many vendors and registries provice SRIDs. This association
is not intended to allow specification of an arbitrary SRID, but provides a
way to reference an SRID in the European Pertroleum Survey Group's (EPSG) SRID
database. EPSG numbers are a de-facto standard for CRS reference and are very
commonly used. EPSG numbers can be searched and referenced in a number of
places including [@?EPSG.io] and [@?SpatialReference.org]. The
unsigned-integer form (major type 0) signifies the EPSG authority: the value
is interpreted as an EPSG SRID.

# IANA Considerations

IANA is requested to allocate a tag from the Specification Required space,
with the present document as the specification reference.

| Tag | Data Item | Semantics |
|-----|-----------|-------------------------------------------------------|
| 104 | multiple  | Geographic Coordinate Reference System WKT or EPSG number |

# Security Considerations

The security considerations of [@!RFC8949] apply; the tag introduced here are
not expected to raise security considerations beyond those.

<reference anchor='CBOR-GC' target='https://github.com/allthingstalk/cbor/blob/master/CBOR-Tag103-Geographic-Coordinates.md'>
    <front>
        <title>CBOR Geographic Coordinates</title>
        <author>
            <organization>AllThingsTalk</organization>
        </author>
    </front>
</reference>

<reference anchor='WKT' target='http://www.opengis.net/doc/IS/wkt-crs/1.0'>
    <front>
        <title>Geographic information - Well-known text representation of coordinate reference systems</title>
        <author>
            <organization>Open Geospatial Consortium</organization>
        </author>
        <date month='May' year='2015'/>
    </front>
</reference>

<reference anchor='EDS1' target='https://doi.org/10.5281/ZENODO.1326873'>
    <front>
        <title>Earthlab/Earth-Analytics-R-Course: Earth Analytics Course In The R Programming Language</title>
        <author initials='L.' surname='Wasser'/>
        <date month='August' year='2018'/>
    </front>
    <seriesInfo name='DOI' value='10.5281/ZENODO.1326873'/>
</reference>

<reference anchor='EPSG.io' target='https://epsg.io'>
    <front>
        <title>MapTiler EPSG Reference Website</title>
        <author>
            <organization>MapTiler</organization>
        </author>
    </front>
</reference>

<reference anchor='SpatialReference.org' target='http://spatialreference.org'>
    <front>
        <title>Spatial Reference Website</title>
        <author>
            <organization>SpatialReference.org</organization>
        </author>
    </front>
</reference>

<reference anchor='OGC' target='http://www.opengeospatial.org/'>
    <front>
        <title>The Open Geospatial Consortium</title>
        <author>
            <organization>Open Geospatial Consortium</organization>
        </author>
    </front>
</reference>

<reference anchor='SR-ORG-7428' target='http://spatialreference.org/ref/sr-org/7428/'>
    <front>
        <title>SR-ORG:7428 WGS 84 (3D EGM96 geoid height)</title>
        <author>
            <organization>SpatialReference.org</organization>
        </author>
    </front>
</reference>

{backmatter}

# Examples

The examples use the CBOR diagnostic notation defined in Section 8 of
[@!RFC8949], with each encoding shown as annotated hexadecimal.

CRS for EPSG:4326, the World Geodetic System 1984 horizontal coordinate system
used by GPS satellites, specified using an EPSG SRID.

```
D8 68      # Coordinate Reference System  - tag(104)
   19 10E6 # EPSG:4326                    - unsigned(4326)

# Diagnostic notation: 104(4326)
```

CRS for WGS 84 3D EGM96 geoid height [@?SR-ORG-7428] specified using WKT.

```
D8 68             # Coordinate Reference System  - tag(104)
   79 0191        # OGC WKT                      - text(401)
      47454F4743535B225747532038342028
      33442045474D39362067656F69642068
      656967687429222C444154554D5B2257
      6F726C642047656F6465746963205379
      7374656D2031393834222C5350484552
      4F49445B22574753203834222C363337
      383133372E302C3239382E3235373232
      333536332C415554484F524954595B22
      45505347222C2237303330225D5D2C41
      5554484F524954595B2245505347222C
      2236333236225D5D2C5052494D454D5B
      22477265656E77696368222C302E302C
      415554484F524954595B224550534722
      2C2238393031225D5D2C554E49545B22
      444D53222C302E303030303034383438
      31333638313130393533365D2C415849
      535B2247656F6465746963206C617469
      74756465222C4E4F5254485D2C415849
      535B2247656F6465746963206C6F6E67
      6974756465222C454153545D2C415849
      535B22477261766974792D72656C6174
      656420686569676874222C55502C4155
      54484F524954595B2245505347222C22
      35373733225D5D2C415554484F524954
      595B2245505347222C2234333239225D5D

# Diagnostic notation:
    104("GEOGCS[\"WGS 84 (3D EGM96 geoid height)\",
         DATUM[\"World Geodetic System 1984\",
         SPHEROID[\"WGS 84\",6378137.0,298.257223563,
         AUTHORITY[\"EPSG\",\"7030\"]],
         AUTHORITY[\"EPSG\",\"6326\"]],
         PRIMEM[\"Greenwich\",0.0,AUTHORITY[\"EPSG\",\"8901\"]],
         UNIT[\"DMS\",0.00000484813681109536],
         AXIS[\"Geodetic latitude\",NORTH],
         AXIS[\"Geodetic longitude\",EAST],
         AXIS[\"Gravity-related height\",UP,
         AUTHORITY[\"EPSG\",\"5773\"]],
         AUTHORITY[\"EPSG\",\"4329\"]]")
```
