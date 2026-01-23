## Syntax Notes for YAML Configuration Files

All YAML 1.2.2 constructs are supported. The structures below are specific conventions used in configuration files for defining operation constraints, profiles, and approximation parameters.

### Basic YAML Constructs

YAML files are indentation-sensitive. Using two spaces to indent is recommended.
Strings are given as double-quoted or single-quoted text. Nested strings use the other quote style to avoid escaping.
Use double-quotes first, especially when nesting strings: `"Binary8p2se"`, `"This is a 'nested' string"`.

Key-value pairs are given as mappings: `key: <value>`.

Lists are given as standard YAML sequences:
  `[ <item1>, <item2>, ... ]`
or
   ```
  items:
     - <item1>
     - <item2>
     - ...
  ```

### Tuples

A tuple is given as a string delimited by parentheses:

- `( <first>, <second> )`.
  - In a list of tuples, all tuples must be of the same length.

### Intervals

An interval is given as a string delimited by parentheses containing a bracketed pair:

- `( [<lowerbound>, <upperbound>] )`.
  - `<lowerbound>`, `<upperbound>` each are `<inequality>(<value>)`.
    - `( [>=(0), <(π)] )`, `( [>=(-Sqrt(2)), <=(3/2)] )`.
  - The brackets distinguish intervals from tuples.
  - The bounds always resolve to a floating-point value in the contextual format.
  - The lower bound must resolve to a value less than or equal to the upper bound.
  - Using inequalities at the bounds `>=(-Inf), <=(Inf)` works for all formats.

### Expressions

An expression is given as a string delimited by parentheses:

- `( <expression> )`

Within an expression:

- a list is given:     `( [ <item1>, <item2>, ... ] )`.
- a tuple is given:    `( ( <first>, <second>, ... ) )`.
- an interval is given: `( ([ <lowerbound>, <upperbound> ]) )`.

#### Iterators

Within an expression, string-interpolating iterators may be used to generate lists.

String interpolation is done with `"prefix$(i)suffix"`, where `i` is replaced by each iterator value:

- `( [ (Binary4p$(i)sf for i in [1..3]) ] )`
  - `[ Binary4p1sf, Binary4p2sf, Binary4p3sf ]`.

Multiple substitutions may be done within a single item:

- `( [ (Binary4p$(i)s$(j) for i in [1..2] for j in ["f", "e"]) ] )`
  - `[ Binary4p1sf, Binary4p2sf, Binary4p1se, Binary4p2se ]`.

Two generated lists are concatenated when they are adjacent within a YAML list:

- `[( [Binary6p$(i)sf for i in [1, 3, 4]] ), ( [Binary8p$(i)se for i in [2..4]] )]`
  - `[ Binary6p1sf, Binary6p3sf, Binary6p4sf, Binary8p2se, Binary8p3se, Binary8p4se ]`.
