# Specifying Implementations

version 0.3.0, 2025-12-28 

*this is a work-in-progress, many of the links are placeholders*

[§3]:  see section 3 of the Interim Report[^1]


## Purpose

A conforming implementation supports one or more formats [§3,3.1]. While the standard specifies a uniform set of operations, predicates, functions, and block operations, there are ways that one implementation may differ from another.  The independent specifics of an implmentation are provided to users in a uniform, formalized way.  The formalism is designed to be clear, well-structured, flexible, expressive, and consistent.

## YAML text files

YAML[^2] files are used to describe an implementation. This is a modern format, with the benefit of being easily read by human and by machine. YAML is in wide use, and readily interconverts with JSON. A catalog of operations is provided by the working group[^3]. Every implementation provides at least two files: [**Configurations.yaml**](#configurations)[^4] that details each supported configuration, and [**Profiles.yaml**](#profiles)[^5] that associates formats with the configurations they cover.

An implementation that supports any κ-approximate functions[§4.6] shall provide [**Kappas.yaml**](#kappas)[^6] as described below. An implementation that uses constraints on argument formats for one or more operations shall provide [**Constraints.yaml**](#constraints)[^7] as described below.

Each file begins with metadata. Examples are provided in the sample files.

## StandardOperations

**StandardOperations.yaml** is a catalog of user-level (client) operations, functions, and identifiers specified [§4,5,6]. The outermost level holds metadata and operator categories.  These are the categories:
```
Classification, Comparison, Projection, Extrema, Math, Block, Conversion, Format
```

The next level identifies groups of related operations.  Here are the groups, with subgroups given `group ∋ (subgroup, ...)`.

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

See StandardOperations.yaml[^4] as provided.

## Configurations

**Configurations.yaml** holds one or more configurations. A configuration provides a named suite of supported operations. Every configuration shall specify:

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

See Configurations.yaml[^5] for examples.

## Profiles

**Profiles.yaml** holds one or more profiles. A profile associates one or more P3109 formats with one and only one configuration.

See Profiles.yaml[^6] example.

## Kappas

Kappa.yaml holds one or more kappa-approximate specifications. A kappa-approximate specification provides a uniquely named function with attributes.
Notes may be adjoined using the label **note** in the usual way.  

See Kappas.yaml[^7] example.

- *This file may be omitted only if the implementation does not provide kappa-approximate functions.*

## Constraints

**Constraints.yaml** holds one or more specializations. A specialization constrains one or more function parameters. Each specialization may associate with one or more of the given profiles. 

See Constraints.yaml[^8] example.

*This file may be omitted only if there are no profiles that use constraints on parameters or their formats.*

## Sufficiently Expressive Expressions

We describe a "sufficiently expressive" formalism for specifying the capabilities and restrictions of a P3109 conformant implementation. It allows some YAML fields to contain expressions that determine values and describe constraints.

Expressions are given as strings that begin with "(" and end with ")".

### Identifiers, Numbers, Variables

#### **Identifiers**

- use UTF8 characters
- are case-sensitive
- start with a letter
- allow letters, digits (0-9), and underscores (_).
- are limited to 60 characters

#### **Numbers**

- Integers
  - may be given in decimal, hexadecimal, or binary notaton.
- Floating-points
  - may be given in decimal, scientific, or hexadecimal notation.

#### **Intervals**

bounds are interpreted in the context of a given format.
- closed intervals are given [<lower_bound>, <upper_bound>]
- open intervals are given (<lower_bound>, <upper_bound>)
- closed-open intervals are given [<lower_bound>, <upper_bound>)
- open-closed intervals are given (<lower_bound>, <upper_bound>]

#### **Sets**

Set values are interpreted in the context of a given format.
- the empty set is given {}
- a singleton set is given {<value>}
- a set with multiple explicit values is given {<value_a>, <value_b>, ...}
- a set given by comprehension is given {(<comprehension>)}
- these set operations are supported:
  - union: A ∪ B
  - intersection: A ∩ B
  - difference: A - B
  - symmetric difference: A Δ B
  - cardinality: |A|
- these set predicates are supported:
  - subset: A ⊆ B
  - proper subset: A ⊂ B
  - superset: A ⊇ B
  - proper superset: A ⊃ B
  - membership: x ∈ A, x ∉ A

#### **Variables**

- are permitted
- are assigned using <variable_name> = <value>
  - where <value> is any of {<immediate_value>, <computed_value>, <iterated_value>, <generated_value>} 
  - values are single or collective
    - collective values may be given as any of {interval, set, list}

#### **Comparisions**

- use standard operators: ==, !=, <, <=, >, >=

#### **Selections**

- the n-ary operatiors Minimum(...) and Maximum(...) are supported
- the conditional operator is supported: (<condition> ? <value_if_true> : <value_if_false>)


#### **Logical Operations**
- the logical operators "and" (&), "or" (|), "xor" (⊻), "not" (!) are supported
- the short-circuiting operators &&, || are supported
- all(x in A : <condition>) is supported
- any(x in A : <condition>) is supported
- parentheses may be used for grouping

#### **Conditionals**
- trinary conditionals are supported: (<condition> ? <value_if_true> : <value_if_false>)
- "if-then" expressions are supported: (if <condition> then <value_if_true> end)
- "if-then-else" expressions are supported: (if <condition> then <value_if_true> else <value_if_false> end)
- onlyif, onlyifnot
  
#### **Generation**
- "for" loops are supported: (<expression> for <variable>=<start>.. <end>)
- comprehensions are supported: (x in [1,10] onlyif iseven(x))
 
### **Expressions**
- standard operator precedence is observed
  
### Format-Defined Quantities

For any format, the following are defined:

* **Bitwidth**: total bits.
* **Precision**: significant bits.
* **TrailingBits**: fraction bits.
* **ExponentBits**: exponent bits.
* **ExponentBias**: exponent bias.

### Format-Defined Predicates

The following Booleans are defined; in numeric expressions, `true = 1`, `false = 0`:

* **IsUnsigned**: signedness (σ) is Unsigned.
* **IsSigned**: signedness (σ) is Signed.
* **IsFinite**: domain (Δ) is Finite.
* **IsExtended**: domain (Δ) is Extended.

### Format-Defined Extremal Values

* **MinimumSubnormal**: smallest positive subnormal; if `Precision = 1`, this is `0`.
* **MaximumSubnormal**: largest positive subnormal; if `Precision = 1`, this is `0`.
* **MinimumNormal**: smallest positive normal.
* **MaximumNormal**: largest positive normal.



## ENBF (Extended Backus-Naur Form)

(* Lexical *)
identifier   = letter , { letter | digit | "_" } ;
letter       = "A".."Z" | "a".."z" ;
digit        = "0".."9" ;

integer      = decimal | hexint | binint ;
decimal      = digit , { digit } ;
hexint       = "0x" , hexdigit , { hexdigit } ;
binint       = "0b" , bindigit , { bindigit } ;
hexdigit     = digit | "A".."F" | "a".."f" ;
bindigit     = "0" | "1" ;

float        = decimalfloat | scientific | hexfloat ;
decimalfloat = decimal , "." , { digit } ;
scientific   = (decimal | decimalfloat) , ("e"|"E") , [ "+" | "-" ] , decimal ;
hexfloat     = "0x" , hexdigit , { hexdigit | "." } , ("p"|"P") , [ "+" | "-" ] , decimal ;

number       = float | integer ;

(* Top-level expression *)
expr         = conditional ;

conditional  = logical_or ,
               [ "?" , expr , ":" , expr ] ;

logical_or   = logical_and , { "||" , logical_and } ;
logical_and  = equality ,    { "&&" , equality } ;

equality     = relational , { ("==" | "!=") , relational } ;

relational   = additive ,
               { ("<" | "<=" | ">" | ">=" | "∈" | "∉" | "⊆" | "⊂" | "⊇" | "⊃") , additive } ;

additive     = multiplicative , { ("+" | "-") , multiplicative } ;

multiplicative = unary , { ("*" | "/" | "%") , unary } ;

unary        = [ "-" | "+" | "!" ] , primary ;

primary      = literal
             | identifier
             | set_literal
             | set_comprehension
             | interval
             | quantifier
             | minmax
             | "(" , expr , ")"
             ;

literal      = number | "true" | "false" ;

(* Plain set literal *)
set_literal  = "{" , [ expr , { "," , expr } ] , "}" ;

(* Iteration / comprehension *)
set_comprehension =
    "{" , expr , "for" , identifier , "in" , expr , [ "if" , expr ] , "}" ;

(* Intervals *)
interval     = ( "[" | "(" ) , expr , "," , expr , ( "]" | ")" ) ;

(* Quantifiers *)
quantifier   = ( "all" | "any" ) , "(" , identifier , "in" , expr , ":" , expr , ")" ;

(* Minimum/Maximum n-ary selectors *)
minmax       = ( "Minimum" | "Maximum" ) ,
               "(" , expr , { "," , expr } , ")" ;

(* Predefined names (semantic, not enforced here):
   Bitwidth | Precision | TrailingBits | ExponentBits | ExponentBias
   IsUnsigned | IsSigned | IsFinite | IsExtended
   MinimumSubnormal | MaximumSubnormal | MinimumNormal | MaximumNormal
*)

---

[^1]: [Interim Report](https://github.com/P3109/Public/blob/main/IEEE%20WG%20P3109%20Interim%20Report%20(latest).pdf)
[^2]: ["YAML Standard"](https://yaml.org/spec/1.2.2/)
[^3]: [StandardOperations](https://github.com/P3109/Public/blob/main/Exemplars/StandardOperations.yaml)
[^4]: [Configurations](https://github.com/P3109/Public/blob/main/Exemplars/Configurations.yaml)
[^5]: [Profiles](https://github.com/P3109/Public/blob/main/Exemplars/Profiles.yaml)
[^6]: [Kappas](https://github.com/P3109/Public/blob/main/Exemplars/Kappas.yaml)
[^7]: [Constraints](https://github.com/P3109/Public/blob/main/Exemplars/Constraints.yaml)
