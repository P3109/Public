# Specifying Implementations

Conformant implementations support one or more formats. While the standard specifies a uniform set of predicates, operations, and functions, there are ways that one implementation may differ from another. This appendix provides for the conformant sharing of implementation specific information.

## Introduction

YAML, a modern text file format, is used to characterize implementations in a clear, well-structured, flexible, and consistent way. YAML ([spec](https://yaml.org/spec/1.2.2/)) is case sensitive and indentation aware. Indentation shall use spaces only.

There is a public catalog of functions: **StandardOperations.yaml** ([view on GitHub](https://github.com/P3109/Public/blob/main/Exemplars/StandardOperations.yaml)). This report provides the function signatures (see reference label).

These shall be provided with conforming implementations:

- **Configurations.yaml** — names configurations and provides their format-independent specifics.
- **Profiles.yaml** — associates each configuration with each format that it supports.
- **Constraints.yaml** — constrains formats and projections for each configuration.
- **Kappas.yaml** — characterizes any function approximations (each is named in Configurations.yaml).

All files shall begin with a metadata section.

If an implementation does not provide approximate functions, the Kappas.yaml file shall contain only the metadata section followed by `Empty: true`, and no other content.

If a configuration provides no constrained functions, the Constraints.yaml file shall contain only the metadata section followed by the line `Empty: true`, and no other content.

## Expressions

Expressions are strings delimited by parentheses: `( <expression> )`. A few structured forms are available to simplify the use of YAML files for describing an implementation. They are written using expressions.

Within an expression:

- A sequence (YAML has lists: `[1, 2, 3]`) is given: `( [ <item1>, <item2>, ... ] )`
- A tuple is given: `( <first>, <second>, ... )`
- An interval is given: `( [<lowerbound>, <upperbound>] )`

### Intervals

Bounds are inequality elaborated extended real values. They resolve to floating-point values within the format applied.

The symbols π, e, φ (also as: pi, e, phi), {+, -, *, /, div, mod}, and P3109 math functions are allowed in expressions.

| format | extended real interval | floating-point interval |
|--------|------------------------|-------------------------|
| Binary5p3uf | `( [>=(−π/4), <=(Inf)] )` | `[0.0, 4.0]` |
| Binary6p4se | `( [>=(−55/64), <=(Sqrt(5))] )` | `[−0.8125, 2.25]` |

### Iterators

Within an expression, string-interpolating iterators may be used to generate lists. String interpolation uses `$(value)`.

With *x* set to `5`, `'Binary8p$(x)se'` → `Binary8p5se`.

Notice the modification target is enclosed in single quotes.

Integer ranges are written `( start:stop )`, e.g., `( 1:8 )`.  
To step by other integers, write `( start:step:stop )`.

Ranges and Sequences may be used for iteration:

```
( [ 'Binary4p$(i)sf' for i in 1:3 ] )       →  [Binary4p1sf, Binary4p2sf, Binary4p3sf]
( [ 'Binary8p$(i)se' for i in [1,5,6] ] )   →  [Binary8p1se, Binary8p5se, Binary8p6se]
```

Multiple substitutions may be done within a single item:

```
( [ 'Binary4p$(i)s$(j)' for i in 1:2 for j in ["f", "e"] ] )  →  [Binary4p1sf, Binary4p2se, Binary4p1se, Binary4p2se]
```

Adjacent generated lists are concatenated.

---

## Exemplars

The first section in each file is for metadata. All implementer provided files shall provide these metadata fields (here, "Configurations:" and its "uri:" are to be replaced with the actual filename and location). Additional metadata may be given following the uri. (For the most recent exemplars, see [P3109/Public/Exemplars](https://P3109/Public/Exemplars).)

```yaml
Metadata:
  conformance: "IEEE SA P3109"
  version: "3.2.0"
  provider: "Provider"
  contact:
    email: info@implementer.site
    uri:  https://implementer.site
  implementation:
    name: "release name"
    version: '1.0.0'
    timestamp: 2026-01-23T10:22Z
  Configurations:
    uri: https://implementer.site/P3109/Implementation/v1.0.0/Configurations.yaml
```

---
