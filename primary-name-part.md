# Primary name part (rufname, goes-by, callname, preferred)

In some cultures, an individual is given multi-part names with several name parts of the same type,
but with one of those name parts identified as primary and often used without the others. For example,

- In Germany, some documents have a given name underlined as the "rufname."
- In England, some people have two given names but either "go by" the first or the second such name.
- In Spain, it is common to have two surnames, with a primary surname indicated by the order of the surnames.

This is far from an exhaustive list, but gives a feel for the scope of this usage.


Three proposals have been floated for potentially addressing this topic:

1. two new name part structure types (one for primary given name, one for primary surname).
2. new markup in the PersonalName datatype, perhaps an underscore of asterisk.
3. a name part substructure.

Proposal 1 is not very easily added using the GEDCOM 7 extension mechanisms, and arguably is not backwards compatible (e.g. in the event that whatever additional markup is added is already in use in someone's name).

Proposal 2 was notably taken by GEDCOM-L in its [_RUFNAME extension to 5.5.1](https://genealogy.net/GEDCOM/GEDCOM551%20GEDCOM-L%20Addendum-R2.pdf#page=24).
However, proposal 2 leads to either (a) the same name part appearing multiple times (as both `GIVN` and `_RFNAME`; this is what GEDCOM-L proposes), possibly leading to confusion about the quantity of name parts (usually disambiguable via the PersonalName datatype payload);
or (b) using `_RUFNAME` in place of `GIVN`, likely losing information for applications not understanding the extension.

Proposal 3 can cause information loss through applications that do not understand the extension, but cannot cause confusion about meaning, and is what this document describes.

# YAML files

## Substructure of a name part

The new structure type:

```yaml
%YAML 1.2
---
lang: en-US

type: structure

uri: https://tychonievich.github.io/ged7-extensions/#name-part-qualifier

extension tags:
  - _PART_QUALIFIER

specification:
  - | 
    A type-like descriptor of a name part, giving more details than the name 
    part structure type itself provides.

label: 'Kind or qualifier of a name part'

payload: https://gedcom.io/terms/v7/type-Enum

enumeration set: "https://tychonievich.github.io/ged7-extensions/#enumset-name-part-qualifiers"

substructures:
  "https://gedcom.io/terms/v7/PHRASE": "{0:1}"

superstructures:
  "https://gedcom.io/terms/v7/GIVN": "{0:M}"
  "https://gedcom.io/terms/v7/SURN": "{0:M}"

contact: "https://tychonievich.github.io/ged7-extensions/"
...
```

We allow a `PHRASE` so that a user can indicate that a primary given name has a specific type or name, such as "rufname" or a "preferred name".

## Enumeration set of name part qualifiers

The new enumeration set, currently with only one value. Note that GEDCOM-X provides [a list of other qualifiers](https://github.com/FamilySearch/gedcomx/blob/master/specifications/name-part-qualifiers-specification.md) that might be useful for some applications.


```yaml
%YAML 1.2
---
lang: en-US

type: enumeration set

uri: https://tychonievich.github.io/ged7-extensions/#enumset-name-part-qualifiers

enumeration values:
  - "http://gedcomx.org/Primary"

contact: "https://tychonievich.github.io/ged7-extensions/"
...
```


## Enumeration values

This enumeration value is already defined in the GEDCOM-X specification, so we re-use its URI and specification.


```yaml
%YAML 1.2
---
lang: en-US

type: enumeration

uri: http://gedcomx.org/Primary

extension tags:
  - _GX_PRIMARY

specification:
  - A designation for the name of most prominent in importance among the names
    of that type (e.g., the primary given name).

value of:
  - "https://tychonievich.github.io/ged7-extensions/#enumset-name-part-qualifiers"

contact: "https://tychonievich.github.io/ged7-extensions/"
...
```

# Altered Specification

From the above YAML files, we can derive the following altered structure definitions,
using the following URI prefixes:

| Short Prefix | URI Prefix |
|--------------|------------|
| `g7`         | `https://gedcom.io/terms/v7/` |
| `gx`         | `http://gedcomx.org/` |
| `ext`        | `https://tychonievich.github.io/ged7-extensions/` |

## Schema

Per the GEDOCM 7 specification, the tags used for documented extension structures may be changed to avoid tag collisions between independent extensions. However, the following tags are recommended:

```gedcom
0 HEAD
1 GEDC
2 VERS 7.0
1 SCHMA
2 TAG _PART_QUALIFIER https://tychonievich.github.io/ged7-extensions/#name-part-qualifier
2 TAG _GX_PRIMARY http://gedcomx.org/Primary
```

## Structure Organization

### Substructures

#### `PERSONAL_NAME_PIECES` :=

```gedstruct
n NPFX <Text>                              {0:M}  g7:NPFX
n GIVN <Text>                              {0:M}  g7:GIVN
  +1 _PART_QUALIFIER <Enum>                {0:M}  ext:name-part-qualifier
     +2 PHRASE <Text>                      {0:1}  g7:PHRASE
n NICK <Text>                              {0:M}  g7:NICK
n SPFX <Text>                              {0:M}  g7:SPFX
n SURN <Text>                              {0:M}  g7:SURN
  +1 _PART_QUALIFIER <Enum>                {0:M}  ext:name-part-qualifier
     +2 PHRASE <Text>                      {0:1}  g7:PHRASE
n NSFX <Text>                              {0:M}  g7:NSFX
```

## Structure Meaning

### Structure Types

#### `_PART_QUALIFIER` (Name Part Qualifier) `<ext:name-part-qualifier>

A type-like descriptor of a name part, giving more details than the name 
    part structure type itself provides.

## Enumeration Values

### `<ext:enumset-name-part-qualifiers>`

| Value | Meaning |
|-------|---------|
| `_GX_PRIMARY` | A designation for the name of most prominent in importance among the names of that type (e.g., the primary given name). |

