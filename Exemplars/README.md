
# Specifying Implementations

version 0.3.2, 2025-12-29 

*this is work-in-progress material*

[§3]:  see section 3 of the Interim Report[^1]


## Purpose

A conforming implementation supports one or more formats [§3,3.1]. While the standard specifies a uniform set of operations, predicates, functions, and block operations, there are ways that one implementation may differ from another.  The independent specifics of an implmentation are provided to users in a uniform, formalized way.  The formalism is designed to be clear, well-structured, flexible, expressive, and consistent.

## YAML text files

YAML[^2] files are used to describe an implementation. This is a widely used, modern format; it is easily read by humans and easily parsed by software. YAML supports JSON schemas[^3], and, with few restrictions, interconverts with JSON. 

Every implementation provides at least two files: [**Configurations.yaml**](#configurations)[^4] that details each supported configuration, and [**Profiles.yaml**](#profiles)[^5] that associates formats with the configurations they cover. An implementation that supports any κ-approximate functions[§4.6] shall provide [**Kappas.yaml**](#kappas)[^6] as described below. An implementation that uses constraints on argument formats for one or more operations shall provide [**Constraints.yaml**](#constraints)[^7] as described below.

Each file begins with metadata. Examples are provided in the sample files.

## StandardOperations

[**StandardOperations.yaml**](#standardops)[^8] is a catalog of user-level (client) operations, functions, and identifiers specified [§4,5,6]. The outermost level holds metadata and operator categories.  These are the categories:
```
Classification, Comparison, Projection, Extrema, Math, Block, Conversion, Format
```

The next level identifies groups of related operations.  Here are the groups and their subgroups.

```
Classification:
  predicates, enumeration, identification

Comparison:
  compare, order

Extrema:
  value, magnitude, bound

Projection:
  rounding, saturation

Math:
  sign, arithmetic, transcendental

Block:
  extrema ∋ (value, magnitude, bound)
  math ∋ (sign, arithmetic, transcendental)
  reduce
  convert

Conversion:
  P3109 ∋ (Convert)
  IEEE754 ∋ (ConvertToIEEE754, ConvertFromIEEE754)

Format:
  defining, derived, extrema, value
```

- Some entries require an attribute be given (e.g. a P3109 format). Attributes are introduced with a meaningful label e.g. **bits**.
- Some entries have notes giving additional information. Notes are introduced with  the label **note**.

## Configurations

**Configurations.yaml**[^4] holds one or more configurations. A configuration provides a named suite of supported operations. Every configuration shall specify:

 - at least one of these two IEEE 754 formats: `binary32` or `binary64`.

 - at least one stochastic rounding mode: `StochasticA` or `StochasticB` or `StochasticC`.
   -  Stochastic rounding modes shall provide an expression for the number of pseudorandom bits used.

 - Block sizes for core and supported blocks (expressed as `elements` counts in the YAML examples; if block size is in bytes, `elements` SHALL equal the byte-count). 

All expressions are given as strings that begin with "(" and end with ")".

Expressions may use:

- integer and floating-point constants

- the symbolic values: `Inf`, `-Inf, `NaN`
- values taken with respect to the current format: 
    `Bitwidth`, `Precision`, `ExponentBits`, `TrailingBits`, `ExponentBias`

- operators: `+`, `-`, `*`, `÷`, `%`, `/`, `^`, `Trunc`, `Floor`, `Ceil`, `Minimum`, and `Maximum`.

- lists may be written as expressions, e.g. "(elements = [32, 48, 64])", or directly as YAML sequences, e.g. `elements: [32, 48, 64]`.

- intervals "(range = [-4.0, 4.0])", "(range = [[-Inf, -4.0), (4.0, Inf]])".
  - '[', ']' denote closed bounds; '(', ')' denote open bounds.

- iteration "(elements = 2^i for i=4..24)"
  
Kappa-approximate functions are included here by reference to their names as given in Kappas.yaml.

All attribute values shall be present.  

Notes may be adjoined using the label **note** in the usual way.

## Profiles

**Profiles.yaml**[^5] holds one or more profiles. A profile associates one or more P3109 formats with one and only one configuration.

## Kappas

**Kappas.yaml**[^6] holds one or more kappa-approximate specifications. A kappa-approximate specification provides a uniquely named function with attributes.
Notes may be adjoined using the label **note** in the usual way.  

- *This file may be omitted only if the implementation does not provide kappa-approximate functions.*

## Constraints

**Constraints.yaml**[^7] holds one or more specializations. A specialization constrains one or more function parameters. Each specialization may associate with one or more of the given profiles. 

*This file may be omitted only if there are no profiles that use constraints on parameters or their formats.*

---

[^1]: [Interim Report](https://github.com/P3109/Public/blob/main/IEEE%20WG%20P3109%20Interim%20Report%20(latest).pdf)
[^2]: ["YAML Standard"](https://yaml.org/spec/1.2.2/)
[^3]: ["JSON Schema"](https://json-schema.org/draft/2020-12)
[^4]: [Configurations](https://github.com/P3109/Public/blob/main/Exemplars/Configurations.yaml)
[^5]: [Profiles](https://github.com/P3109/Public/blob/main/Exemplars/Profiles.yaml)
[^6]: [Kappas](https://github.com/P3109/Public/blob/main/Exemplars/Kappas.yaml)
[^7]: [Constraints](https://github.com/P3109/Public/blob/main/Exemplars/Constraints.yaml)
[^8]: [StandardOperations](https://github.com/P3109/Public/blob/main/Exemplars/StandardOperations.yaml)
