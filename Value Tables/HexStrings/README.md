```
These files give the code points and corresponding values for all formats for each bitwidth K shown.
Each file is stored as a .csv with three labeled columns: codepoint, value, subnormal.
  subnormal values have an "!" in the subnormal column.

The values are provided in hexadecimal scientific notation (sprintf "%a" format).

  sprintf("%a", 3.5);
  output: "0x1.cp+1"
  
  How to read that:
    0x means hexadecimal
    1.c is the significand in base 16
    p+1 means “multiply by 2¹”
    (p uses a binary exponent, not decimal)
  So:
    0x1.cp+1 = 1.c₁₆ × 2¹ = 3.5
```
