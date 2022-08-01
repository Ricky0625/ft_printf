# ft_printf

Recode printf. Learned what is and how to implement variadic functions.

`man printf` or `man 3 printf` to learn more about printf and the flags.

## Supported flags & conversion type

Flags:

1. \- (left align)
2. 0 (zero padding)
3. . (precision)
4. \# (alternative form, works with x & X only)
5. [space] (prepend space if value is positive, else prepend '-')
6. \+ (prepend '+' if value is positive, else prepend '-')

Conversion type:

1. c (character)
2. s (string)
3. p (address)
4. d (decimal/integer)
5. i (integer)
6. u (unsigned)
7. x (hexadecimal in lowercase)
8. X (hexadecimal in uppercase)
9. % (percent) 
